----
title: LeetCode：链表中倒数第 k 个节点
date: 2021-07-02 23:05:36
categories: 编程
tags: 
- Go
- LeetCode
toc: true
----

## 题目

输入一个链表，输出该链表中倒数第 `k` 个节点。为了符合大多数人的习惯，本题从 `1` 开始计数，即链表的尾节点是倒数第 `1` 个节点。

例如，一个链表有 `6` 个节点，从头节点开始，它们的值依次是 `1、2、3、4、5、6`。这个链表的倒数第 `3` 个节点是值为 `4` 的节点。

<!-- more -->

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof

## 代码

### Go

```go
package main

import "fmt"

type ListNode struct {
	Val  int
	Next *ListNode
}

func getKthFromEnd(head *ListNode, k int) *ListNode {
	var nodes map[int]*ListNode
	nodes = make(map[int]*ListNode)
	n := 0

	for head != nil {
		nodes[n] = head
		head = head.Next

		n++
	}

	return nodes[n-k]
}

func main() {
	fmt.Println(getKthFromEnd(&ListNode{1, &ListNode{2, &ListNode{3, &ListNode{4, &ListNode{5, nil}}}}}, 2))
}
```