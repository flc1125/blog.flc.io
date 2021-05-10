----
title: LeetCode：叶子相似的树
date: 2021-5-10 12:53:23
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
- 二叉树
toc: true
----

## 题目

请考虑一棵二叉树上所有的叶子，这些叶子的值按从左到右的顺序排列形成一个 **叶值序列** 。

![](https://s.flc.io/2021-05-10-12-54-15.png)

举个例子，如上图所示，给定一棵叶值序列为 `(6, 7, 4, 9, 8)` 的树。

如果有两棵二叉树的叶值序列是相同，那么我们就认为它们是 **叶相似** 的。

如果给定的两个根结点分别为 `root1` 和 `root2` 的树是叶相似的，则返回 `true`；否则返回 `false` 。

<!-- more -->

**示例 1：**

![](https://s.flc.io/2021-05-10-12-55-06.jpg)

```
输入：root1 = [3,5,1,6,2,9,8,null,null,7,4], root2 = [3,5,1,6,7,4,2,null,null,null,null,null,null,9,8]
输出：true
```

**示例 2：**

```
输入：root1 = [1], root2 = [1]
输出：true
```

**示例 3：**

```
输入：root1 = [1], root2 = [2]
输出：false
```

**示例 4：**

```
输入：root1 = [1,2], root2 = [2,2]
输出：true
```

**示例 5：**

![](https://s.flc.io/2021-05-10-12-55-32.jpg)

```
输入：root1 = [1,2,3], root2 = [1,3,2]
输出：false
```

**提示：**

- 给定的两棵树可能会有 `1` 到 `200` 个结点。
- 给定的两棵树上的值介于 `0` 到 `200` 之间。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/leaf-similar-trees

## 代码

### Go

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

type TreeNode struct {
	Val   int
	Left  *TreeNode
	Right *TreeNode
}

func leafSimilar(root1 *TreeNode, root2 *TreeNode) bool {
	leaf1, leaf2 := getLeaf(root1), getLeaf(root2)

	return strings.Join(leaf1, "#") == strings.Join(leaf2, "#")
}

func getLeaf(root *TreeNode) []string {
	var result []string

	if root.Left == nil && root.Right == nil {
		result = append(result, strconv.Itoa(root.Val))
	}

	if root.Left != nil {
		result = append(result, getLeaf(root.Left)...)
	}

	if root.Right != nil {
		result = append(result, getLeaf(root.Right)...)
	}

	return result
}

func main() {
	fmt.Println(leafSimilar(
		&TreeNode{
			3,
			&TreeNode{5, &TreeNode{6, nil, nil}, &TreeNode{2, &TreeNode{7, nil, nil}, &TreeNode{4, nil, nil}}},
			&TreeNode{1, &TreeNode{9, nil, nil}, &TreeNode{8, nil, nil}},
		},
		&TreeNode{
			3,
			&TreeNode{5, &TreeNode{6, nil, nil}, &TreeNode{7, nil, nil}},
			&TreeNode{1, &TreeNode{4, nil, nil}, &TreeNode{2, &TreeNode{9, nil, nil}, &TreeNode{8, nil, nil}}},
		},
	))
}
```