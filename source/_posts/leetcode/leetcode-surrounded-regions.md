----
title: LeetCode：被围绕的区域
date: 2021-03-25 00:22:03
categories: LeetCode
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个 `m x n` 的矩阵 `board` ，由若干字符 `'X'` 和 `'O'` ，找到所有被 `'X'` 围绕的区域，并将这些区域里所有的 `'O'` 用 `'X'` 填充。

**示例 1：**

![](https://s.flc.io/2021-03-25-00-24-16.png)

<!-- more -->

```
输入：board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
输出：[["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
解释：被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。 任何不在边界上，或不与边界上的 'O' 相连的 'O' 最终都会被填充为 'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。
```

**示例 2：**

```
输入：board = [["X"]]
输出：[["X"]]
```

**提示：**

- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 200`
- `board[i][j]` 为 `'X'` 或 `'O'`


> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/surrounded-regions

## 代码

### Go

```go
package main

import (
	"fmt"
	"strconv"
)

type point struct {
	val    string
	x, y   int
	isTrue bool
}

var m, n int

func solve(board [][]byte) [][]byte {
	m, n = len(board), len(board[0])
	var points map[string]*point
	points = make(map[string]*point, m*n)

	// 转换为点定位
	for y := 1; y <= m; y++ {
		for x := 1; x <= n; x++ {
			points[getPointKey(x, y)] = &point{
				val:    string(board[y-1][x-1]),
				x:      x,
				y:      y,
				isTrue: false,
			}
		}
	}

	// 先找出边界的O
	for _, v := range points {
		// 先找出边界的O
		if v.val == "O" && (v.x == 1 || v.y == 1 || v.x == n || v.y == m) {
			v.isTrue = true
		}
	}

	// 通过边界为0的，递归找出相邻的
	for _, v := range points {
		if v.isTrue {
			checkAdjoin(v, points)
		}
	}

	for _, v := range points {
		if v.isTrue == false {
			v.val = "X"
		}
	}

	// 整合
	for i := 0; i < m; i++ {
		for j := 0; j < n; j++ {
			board[i][j] = []byte(points[getPointKey(j+1, i+1)].val)[0]
		}
	}

	return board
}

func checkAdjoin(p *point, points map[string]*point) {
	checkIsTrue(points, p.x-1, p.y)
	checkIsTrue(points, p.x+1, p.y)
	checkIsTrue(points, p.x, p.y-1)
	checkIsTrue(points, p.x, p.y+1)
}

func checkIsTrue(points map[string]*point, x int, y int) {
	if x < 1 || x > n || y < 1 || y > m {
		return
	}

	if p := points[getPointKey(x, y)]; p.isTrue == false && p.val == "O" {
		p.isTrue = true
		checkAdjoin(p, points)
	}
}

func getPointKey(x int, y int) string {
	return strconv.Itoa(x) + "_" + strconv.Itoa(y)
}

func main() {
	fmt.Println(solve([][]byte{
		{88, 88, 88, 88, 88},
		{88, 79, 79, 88, 88},
		{88, 88, 79, 88, 88},
		{88, 79, 88, 88, 88},
	}))
	// fmt.Println(solve([][]byte{
	// 	{79, 79, 79},
	// 	{79, 79, 79},
	// 	{79, 79, 79},
	// }))
}
```

> 先转成点，在处理；思路做复杂了~