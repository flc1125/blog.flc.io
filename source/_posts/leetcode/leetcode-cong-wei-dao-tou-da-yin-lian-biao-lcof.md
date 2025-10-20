----
title: LeetCode：从尾到头打印链表
date: 2021-05-11 22:21:08
categories: 编程
tags: 
- Go
- LeetCode
- 链表
toc: true
----

## 题目

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

**示例 1：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

<!-- more -->

**限制：**

- `0 <= 链表长度 <= 10000`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof

## 代码

### Go

```go
package main

import "fmt"

/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */

type ListNode struct {
	Val  int
	Next *ListNode
}

func reversePrint(head *ListNode) []int {
	if head == nil {
		return []int{}
	}

	var result []int

	result = append([]int{head.Val}, result...)

	if head.Next != nil {
		result = append(reversePrint(head.Next), result...)
	}

	return result
}

func main() {
	fmt.Println(reversePrint(&ListNode{
		2, &ListNode{3, &ListNode{5, nil}},
	}))
}
```