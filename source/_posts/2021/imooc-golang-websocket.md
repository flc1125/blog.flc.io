----
title: 笔记：GO 实现千万级 WebSocket 消息推送服务
date: 2021-05-24 21:45:05
updated: 2021-05-28 23:25:39
categories: [编程,后端]
tags: 
- Go
- 笔记
toc: true
----

![](https://s.flc.io/2021-05-28-00-06-23.png)
## 前言

团队内组织 Golang 的学习，这是第四周学习内容：完成课程的学习、代码编写与心得总结。

**相关课程**

https://www.imooc.com/learn/1025

## 内容

### 弹幕技术复杂度

**假设 1 个直播间：**

- 在线人数：100万人
- 发送弹幕：1000条/秒
- 推送频率：100万 * 1000条/秒 = **10亿条/秒**

<!-- more -->

**假设 N 个直播间：**

- 推送频率：**N * 10亿条/秒**

**拉模式与推模式的区别：**

拉模式：

- 若更新频率低，大多数请求是无效的
- 若在线用户数多，服务的查询负载高
- 定时轮询，无法满足时效性

推模式：

- 仅更新才推送
- 需维护大量的长连接
- 数据更新后，立即推送

基于 websocket 推送

- 浏览器支持 socket
- 基于 TCP 协议，安全可靠
- 生态工具良好，低成本接入

### WebSocket 协议

**通讯流程**

![](https://s.flc.io/2021-05-24-22-49-43.png)

**传输原理**

- 协议升级后，复用 HTTP 底层的 socket 协议完成通讯
- message 底层被分割成多个 frame 帧传输
- 编程只需关心 message，无需关心 frame
- 框架底层进行 TCP 网络的 I/O，websocket 协议的解析

### Demo 实操

Demo 地址：https://github.com/flc1125/go-websocket-chat-demo

> 基于课程调整为多客户端的聊天工具。


### 千万级弹幕技术难点

**技术难点**

内核瓶颈

- 推送量大：100W在线 * 10条/秒 = 10000万条/秒
- 内核瓶颈：Linux 内核发送 TCP 的极限包频 ≈ 100万/秒

锁瓶颈

- 需要在线维护用户集合（100W 在线用户），通常是一个字段类型
- 推送即为一个遍历整个集合，顺序发送，耗时极长
- 推送期间，客户端仍旧上/下线，需要上锁

CPU 瓶颈

- 浏览器与服务器传输方式，通常采用 `JSON` 方式
- JSON 编码非常耗费 CPU 资源
- 向 100W 用户推送 1 次消息，需要 100W 次 json encode

**解决方案**

内核瓶颈

- 优化原理：减少网络小包的发送
- 优化方案：将 1s 内的 N 条消息，合并为 1 条消息发送；合并后的发送秒次数 = 在线连接送

锁瓶颈

- 优化原理：大锁拆小锁
- 优化方案：
  - 连接打散到多个集合，每个集合有自己的锁
  - 多线程并发推送多个集合，避免锁竞争
  - 读写锁取代互斥锁，多个推送任务可并发遍历相同的集合

CPU 瓶颈

- 优化原理：减少重复计算
- 优化方案：
  - JSON 编码前置，1 次消息编码 + 100W 次推送
  - 合并消息前置，N 条消息合并只编码 1 次

### 千万级弹幕技术架构

**单机架构**

![](https://s.flc.io/2021-05-28-00-13-42.png)

单机瓶颈

- 维护海量连接，占用内存
- 消息推送，瞬时 CPU 压力大
- 消息推送瞬时带宽达 400~600MB，主要瓶颈

**分布式架构**

网关集群

![](https://s.flc.io/2021-05-28-22-54-04.png)

逻辑集群

- 基于 HTTP/2 协议向网关集群分发消息
  - HTTP/2 支持连接复用，用作 RPC 性能更佳
- 基于 HTTP/1 对外提供 API（其实也可以通过 HTTP/2 且性能更优）

整体架构

![](https://s.flc.io/2021-05-28-22-58-20.png)

> 说了跟没说一样……

### 课外补充：`select` 语句

示例：

```go
package main

import (
	"fmt"
	"time"
)

var (
	a = make(chan int)
	b = make(chan int)
	c = make(chan int)
)

func worker() {
	for {
		select {
		case aa := <-a:
			fmt.Println("a:", aa)
		case bb := <-b:
			fmt.Println("b:", bb)
		case <-c:
			fmt.Println("c:", <-c)  // 注意该值
		}
	}
}

func producer() {
	for i := 0; i <= 30; i++ {
		go func(i int) {
			if i <= 10 {
				a <- i
			} else if i > 10 && i <= 20 {
				b <- i
			} else {
				c <- i
			}
		}(i)
	}
}

func main() {
	go worker()

	go producer()

	time.Sleep(time.Second * 1)
}
```

输出

```
a: 1
b: 13
a: 0
c: 22
b: 11
c: 24
c: 26
a: 3
b: 12
a: 4
b: 14
a: 5
b: 15
c: 27
a: 6
a: 7
c: 29
b: 16
b: 17
b: 18
b: 19
a: 8
a: 9
b: 20
a: 10
a: 2
```

## 总结

- 课程内容理论尚可，实践偏弱，千万级弹幕的系统具体实现几乎没有；演练的 Demo 也仅支持单人弹幕。
- websocket 是基于 HTTP 请求的 upgrade 版
- channel 应用：在客户端长连接到服务端、断开连接、接收消息、发送消息等均可使用

## 参考

- websocket 示例：https://github.com/gorilla/websocket/tree/master/examples