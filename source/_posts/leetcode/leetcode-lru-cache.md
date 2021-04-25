----
title: LeetCode：LRU 缓存机制
date: 2021-4-25 20:26:29
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

> **前言：**
> 
> 推荐官方题解：https://leetcode-cn.com/problems/lru-cache/solution/lruhuan-cun-ji-zhi-by-leetcode-solution/
> 
> **注：对高性能的 LRU 理解，会有一定提升作用**

运用你所掌握的数据结构，设计和实现一个  **`LRU` (最近最少使用) 缓存机制** 。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以正整数作为容量 `capacity` 初始化 LRU 缓存
- · 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

<!-- more -->
 
**进阶：** 你是否可以在 `O(1)` 时间复杂度内完成这两种操作？

**示例：**

```
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

**提示：**

- `1 <= capacity <= 3000`
- `0 <= key <= 3000`
- `0 <= value <= 10^4`
- 最多调用 `3 * 104` 次 `get` 和 `put`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/lru-cache

## 代码

### Go

如下题解：性能无官方性能优。

```go
package main

import "fmt"

type LRUCache struct {
	Capacity int
	Keys     []int
	Items    map[int]int
}

func Constructor(capacity int) *LRUCache {
	return &LRUCache{capacity, []int{}, map[int]int{}}
}

func (this *LRUCache) Get(key int) int {
	_, value := this.Has(key)

	return value
}

func (this *LRUCache) Put(key int, value int) {
	if ok, _ := this.Has(key); !ok {
		this.Clean()
		this.Keys = append(this.Keys, key)
	}

	this.Items[key] = value
}

func (this *LRUCache) Has(key int) (bool, int) {
	if value, ok := this.Items[key]; ok {
		if index := indexOf(key, this.Keys); index >= 0 {
			this.Keys = append(this.Keys[0:index], this.Keys[(index+1):]...)
			this.Keys = append(this.Keys, key)
		}

		return true, value
	}

	return false, -1
}

func (this *LRUCache) Clean() {
	if len(this.Keys) >= this.Capacity {
		first := this.Keys[0]

		this.Keys = this.Keys[1:]
		delete(this.Items, first)
	}
}

func indexOf(value int, items []int) int {
	for k, v := range items {
		if v == value {
			return k
		}
	}

	return -1
}

func main() {
	cache := Constructor(2)
	// cache.Put(1, 1)
	// cache.Put(2, 2)
	// cache.Put(3, 3)
	// cache.Put(4, 4)
	// cache.Put(3, 5)

	cache.Put(1, 1)
	cache.Put(2, 2)
	cache.Get(1)
	// cache.Put(3, 3)
	// cache.Get(2)

	fmt.Println(cache)
}
```
