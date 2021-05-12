----
title: LeetCode：对称的二叉树
date: 2021-05-12 23:03:42
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
- 二叉树
toc: true
----

## 题目

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

<!-- more -->

但是下面这个 `[1,2,2,null,3,null,3]` 则不是镜像对称的:

```
    1
   / \
  2   2
   \   \
   3    3
```

**示例 1：**

```
输入：root = [1,2,2,3,4,4,3]
输出：true
```

**示例 2：**

```
输入：root = [1,2,2,null,3,null,3]
输出：false
```

**限制：**

- `0 <= 节点个数 <= 1000`

**注意：**本题与主站 101 题相同：https://leetcode-cn.com/problems/symmetric-tree/

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof

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

func isSymmetric(root *TreeNode) bool {
	if root == nil {
		return true
	}
	
	return isValid(root.Left, root.Right)
}

func isValid(left *TreeNode, right *TreeNode) bool {
	if left == nil && right == nil {
		return true
	}

	if left == nil || right == nil || left.Val != right.Val {
		return false
	}

	return isValid(left.Left, right.Right) && isValid(left.Right, right.Left)
}

func main() {
	fmt.Println(isSymmetric(&TreeNode{
		1,
		&TreeNode{2, &TreeNode{3, nil, nil}, &TreeNode{4, nil, nil}},
		&TreeNode{2, &TreeNode{4, nil, nil}, &TreeNode{3, nil, nil}},
	}))
}
```