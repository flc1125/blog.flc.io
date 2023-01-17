----
title: LeetCode：路径总和
date: 2021-05-13 23:35:14
categories: LeetCode
tags: 
- Go
- LeetCode
- 二叉树
toc: true
----

## 题目

给你二叉树的根节点 `root` 和一个表示目标和的整数 `targetSum` ，判断该树中是否存在 **根节点到叶子节点** 的路径，这条路径上所有节点值相加等于目标和 targetSum 。

**叶子节点** 是指没有子节点的节点。

<!-- more -->

**示例 1：**

![](https://s.flc.io/2021-05-13-23-34-10.png)

```
输入：root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22
输出：true
```

**示例 2：**

![](https://s.flc.io/2021-05-13-23-34-20.png)

```
输入：root = [1,2,3], targetSum = 5
输出：false
```

**示例 3：**

```
输入：root = [1,2], targetSum = 0
输出：false
```

**提示：**

- 树中节点的数目在范围 `[0, 5000]` 内
- `-1000 <= Node.val <= 1000`
- `-1000 <= targetSum <= 1000`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/path-sum

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
	Val         int
	Left, Right *TreeNode
}

func hasPathSum(root *TreeNode, targetSum int) bool {
	return Sum(root, targetSum)
}

func Sum(root *TreeNode, num int) bool {
	if root == nil {
		return false
	}

	num -= root.Val

	if root.Left == nil && root.Right == nil && num == 0 {
		return true
	}

	if root.Left != nil {
		if Sum(root.Left, num) {
			return true
		}
	}

	if root.Right != nil {
		if Sum(root.Right, num) {
			return true
		}
	}

	return false
}

func main() {
	// fmt.Println(hasPathSum(
	// 	&TreeNode{5, &TreeNode{4, &TreeNode{11, nil, nil}, nil}, &TreeNode{8, nil, nil}},
	// 	20,
	// ))

	fmt.Println(hasPathSum(
		&TreeNode{-2, nil, &TreeNode{-3, nil, nil}},
		-5,
	))
}
```