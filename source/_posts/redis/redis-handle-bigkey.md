----
title: Redis：处理 bigkey
date: 2020-11-20 14:20
categories: [后端]
tags:
- Redis
- 笔记
toc: true
----

## 危害

- **内存空间不均匀（平衡）：**如 Redis Cluster 中，bigkey 会造成节点的内存空间分布不均匀
- **超时阻塞：**由于 Redis 单线程的特性，操作 bigkey 会较耗时，意味着阻塞 Redis 可能性较大。
- **网络阻塞：**获取 bigkey 的网络传输较大，不仅影响客户端，而且影响其他节点；如：bigkey 为 1MB，每秒 1000次，每秒产生的流量 1000MB/s

<!-- more -->
## 如何发现


- `redis-cli --bigkeys` 可统计 bigkey 的分布
- 判定一个 key 是否为 bigkey
   - 执行 `debug object key` 查看 `serializedlength` 属性即可，该值为对应 value 序列化后的字节数；其中 `encoding` 可查看 key 的数据类型。
   - `strlen key` 可查看 key 的字节数；部分数据类型不支持

> **注：**`serializedlength` 不代表真实的字节大小，返回的是对象使用 RDB 编码序列化后的长度，值会偏小。但对排查 bigkey 有一定辅助作用，因为并不是每种数据类型都支持 `strlen` 的方法。

**实际生产环境发现 bigkey 的两种方式：**

- **被动收集：**客户端记录异常后记录 key，统一排查
- **主动监测（推荐）：**`scan+debug object`，如果怀疑存在 bigkey，可以使用 scan 命令渐进的扫描所有 key，分别计算每个 key 的 serializedlength ，找出 bigkey。

**开发提醒：**

- 如果键值较多：`scan+debug object` 会比较慢，**可利用 pipeline 机制完成**。
- 对与元素个数较多的数据结构，如：hash、list等，debug object 会比较慢，**存在阻塞 Redis 的可能**。
- **如果有从节点，可以考虑从节点上执行**。

## 如何删除

如发现 bigkey 后，无论什么命令，最都可直接使用 del 将其删除。但实际情况并非如此，不同的数据类型，不同的元素个数等不尽相同，删除可能会造成 redis 阻塞。

### 耗时对比


**删除字符串类型，不同大小的耗时对比：**

| **key 类型** | **512KB** | **1MB** | **2MB** | **5MB** | **10MB** |
| :---: | :---: | :---: | :---: | :---: | :---: |
| string | 0.22ms | 0.31ms | 0.32ms | 0.56ms | 1ms |

**总结：**string 类型随着 value 值的不断增大，删除速度也逐渐减慢。

**删除 hash、list、set、sorted set 四种数据类型，不同数量，不同元素大小的耗时对比：**

| **key 类型** | **10W （8个字节)** | **100W （8个字节）** | **10W （16个字节）** | **100W （16个字节)** | **10W （128个字节）** | **100W （128个字节）** |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| hash | 51ms | 950ms | 58ms | 970ms | 96ms | 2000ms |
| list | 23ms | 134ms | 23ms | 138ms | 23ms | 266ms |
| set | 44ms | 873ms | 58ms | 881ms | 73ms | 1319ms |
| sorted set  | 51ms | 845ms | 57ms | 859ms | 59ms | 969ms |

**总结：**非字符串类型，元素的个数越多，元素越大，删除时间越长。且该删除时间，足够引起 Redis 阻塞。

### 如何优雅删除

如何优雅的删除，且不影响 Redis 阻塞？相关 `scan` 命令如：`sscan`、`hscan`、`zscan`。

- **string 类型：**直接使用 del 命令即可，一般不会产生阻塞。
- **hash、list、set、sorted set 类型：**可使用 `scan` 命令，如：`sscan`、`hscan`、`zscan`
   - **思路：**采用 scan 命令，删除每个元素后，在执行 del key。
   - **切记：**执行最后需删除 del key。

## 最佳实践

- 数据结构合理的选择和设计，如存在 bigkey，可考虑进行拆分
- 数据读取，无需将每个数据都查询出来（如：推荐 hmget 而不是 hgetall）
- Redis 4.0 版本支持 `lazy delete free` 模式，这时操作删除 bigkey 不会阻塞 Redis 
