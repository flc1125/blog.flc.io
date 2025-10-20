----
title: LeetCode：翻转二叉树
date: 2021-05-09 00:05:29
categories: 编程
tags: 
- Go
- LeetCode
- 二叉树
toc: true
----

## 题目

翻转一棵二叉树。

**示例：**

输入：

```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

<!-- more -->

输出：

```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

**备注:**

这个问题是受到 [Max Howell](https://twitter.com/mxcl) 的 [原问题](https://twitter.com/mxcl/status/608682016205344768) 启发的 ：

> 谷歌：我们90％的工程师使用您编写的软件(Homebrew)，但是您却无法在面试时在白板上写出翻转二叉树这道题，这太糟糕了。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/invert-binary-tree

## 代码

### Go

```go
package main

import "fmt"

/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */

type TreeNode struct {
	Val   int
	Left  *TreeNode
	Right *TreeNode
}

func invertTree(root *TreeNode) *TreeNode {
	if root == nil {
		return nil
	}

	r := &TreeNode{root.Val, nil, nil}

	r.Left = invertTree(root.Right)

	r.Right = invertTree(root.Left)

	return r
}

func main() {
	fmt.Println(invertTree(&TreeNode{
		4,
		&TreeNode{2, &TreeNode{1, nil, nil}, &TreeNode{3, nil, nil}},
		&TreeNode{7, &TreeNode{6, nil, nil}, &TreeNode{9, nil, nil}},
	}))

	fmt.Println(invertTree(&TreeNode{}))
}
```