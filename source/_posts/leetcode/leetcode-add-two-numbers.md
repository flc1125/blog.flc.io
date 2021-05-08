----
title: LeetCode：两数相加
date: 2021-05-08 23:27:35
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
- 链表
toc: true
----

## 题目

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

<!-- more -->

**示例 1：**

![](https://s.flc.io/2021-05-08-23-29-40.png)

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

**示例 2：**

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

**示例 3：**

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```

**提示：**

- 每个链表中的节点数在范围 `[1, 100]` 内
- `0 <= Node.val <= 9`
- 题目数据保证列表表示的数字不含前导零

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/add-two-numbers

## 代码

### Go

**方法一：基于数组在转换为链表**

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

func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
	// var l *ListNode
	var r []int

	prev, curr := 0, 0

	for {
		// Validate
		if (l1 == nil) && (l2 == nil) {
			break
		}

		// Handle
		total := value(l1, 0) + value(l2, 0) + prev

		if total >= 10 {
			prev, curr = 1, total-10
		} else {
			prev, curr = 0, total
		}

		r = append(r, curr)

		// End
		if l1 != nil {
			l1 = l1.Next
		}

		if l2 != nil {
			l2 = l2.Next
		}
	}

	if prev > 0 {
		r = append(r, prev)
	}

	return transformNode(r)
}

// 获取值或默认值
func value(v interface{}, d int) int {
	switch value := v.(type) {
	case *ListNode:
		if value == nil {
			return d
		}

		return value.Val
	default:
		return d
	}
}

func transformNode(i []int) *ListNode {
	var result, last, prev *ListNode

	for _, v := range i {
		last = &ListNode{v, nil}

		if result == nil {
			result = last
		} else {
			prev.Next = last
		}

		prev = last
	}

	return result
}

func main() {
	fmt.Println(addTwoNumbers(
		&ListNode{2, &ListNode{4, &ListNode{3, nil}}},
		&ListNode{5, &ListNode{6, &ListNode{4, nil}}},
	))

	fmt.Println(addTwoNumbers(
		&ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, nil}}}}}}},
		&ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, nil}}}},
	))
}
```

**方法二：直接转换为链表**

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

func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
	var result, r_last, r_prev *ListNode

	prev, curr := 0, 0

	for {
		// Validate
		if (l1 == nil) && (l2 == nil) {
			break
		}

		// Handle
		total := value(l1, 0) + value(l2, 0) + prev

		if total >= 10 {
			prev, curr = 1, total-10
		} else {
			prev, curr = 0, total
		}

		r_last = &ListNode{curr, nil}

		if result == nil {
			result = r_last
		} else {
			r_prev.Next = r_last
		}

		r_prev = r_last

		// End
		if l1 != nil {
			l1 = l1.Next
		}

		if l2 != nil {
			l2 = l2.Next
		}
	}

	if prev > 0 {
		r_prev.Next = &ListNode{prev, nil}
	}

	return result
}

// 获取值或默认值
func value(v interface{}, d int) int {
	switch value := v.(type) {
	case *ListNode:
		if value == nil {
			return d
		}

		return value.Val
	default:
		return d
	}
}

func main() {
	fmt.Println(addTwoNumbers(
		&ListNode{2, &ListNode{4, &ListNode{3, nil}}},
		&ListNode{5, &ListNode{6, &ListNode{4, nil}}},
	))

	fmt.Println(addTwoNumbers(
		&ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, nil}}}}}}},
		&ListNode{9, &ListNode{9, &ListNode{9, &ListNode{9, nil}}}},
	))
}
```
