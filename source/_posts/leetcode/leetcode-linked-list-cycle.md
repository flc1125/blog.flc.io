----
title: LeetCode：环形链表（快慢指针）
date: 2021-5-7 19:47:38
categories: 编程
tags: 
- Go
- LeetCode
- 快慢指针
- 链表
toc: true
----

## 题目

给定一个链表，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

如果链表中存在环，则返回 `true` 。 否则，返回 `false` 。


**进阶：**

- 你能用 `O(1)`（即，常量）内存解决此问题吗？

<!-- more -->

**示例 1：**

![](https://s.flc.io/2021-05-07-19-50-06.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![](https://s.flc.io/2021-05-07-19-50-17.png)

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

![](https://s.flc.io/2021-05-07-19-50-29.png)

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

**提示：**

- 链表中节点的数目范围是 `[0, 104]`
- `-10^5 <= Node.val <= 10^5`
- `pos` 为 `-1` 或者链表中的一个 **有效索引** 。


> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/linked-list-cycle

## 代码

### Go

> 反正这个代码在官方通不过，见鬼，这个题目的 `pos` 也是有点米糊。按自己的方式去实现算法

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

func hasCycle(head *ListNode) bool {
	nodes := map[int]int{} // 不知道为啥 map[int]int 官方通不过

	for {
		if head == nil {
			break
		}

		if _, ok := nodes[head.Val]; ok {
			return true
		}

		nodes[head.Val] = head.Val
		head = head.Next
	}

	return false
}

func main() {
	fmt.Println(hasCycle(&ListNode{}))

	fmt.Println(hasCycle(&ListNode{
		Val: 3,
		Next: &ListNode{
			Val: 2,
			Next: &ListNode{
				Val: 0,
				Next: &ListNode{
					Val: -4,
					Next: &ListNode{
						Val: 2,
						Next: &ListNode{
							Val: 10,
						},
					},
				},
			},
		},
	}))
}
```

## 参考

- [进阶算法：快慢指针](https://leetcode-cn.com/problems/linked-list-cycle/solution/huan-xing-lian-biao-by-leetcode-solution/)