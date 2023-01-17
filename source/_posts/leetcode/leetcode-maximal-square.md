----
title: LeetCode：最大正方形
date: 2021-03-17 23:14:48
categories: LeetCode
tags: 
- Go
- LeetCode
toc: true
----

## 题目

在一个由 `'0'` 和 `'1'` 组成的二维矩阵内，找到只包含 `'1'` 的最大正方形，并返回其面积。

**示例 1：**

![](https://s.flc.io/2021-03-17-23-16-55.png)

```
输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：4
```

<!-- more -->

**示例 2：**

![2021-03-17-23-17-12](https://s.flc.io/2021-03-17-23-17-12.png)

输入：matrix = [["0","1"],["1","0"]]
输出：1

**示例 3：**

```
输入：matrix = [["0"]]
输出：0
```

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 300`
- `matrix[i][j]` 为 `'0'` 或 `'1'`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/maximal-square

## 思路

![](https://s.flc.io/2021-03-17-23-21-28.png)

如图将每个数字转换为坐标系位置，如图取最大，可依次循环：**淡红→蓝→绿**，判定是否为正方形，并取最长即可。

## 代码

### Go

> 代码本地运算正常，符合结果；但提交结果却不对，搞不明白 LeetCode ~~

```go
package main

import (
	"fmt"
	"math"
	"strconv"
)

// 点信息
type point struct {
	x, y, value int
}

type sec struct {
	x_min, x_max, y_min, y_max int
}

func maximalSquare(matrix [][]byte) int {
	points := convertPoint(matrix)

	sec := sec{1, len(matrix[0]), 1, len(matrix)}
	r := 0

	for y := sec.y_min; y <= sec.y_max; y++ {
		for x := sec.x_min; x <= sec.x_max; x++ {
			point := points[strconv.Itoa(x)+"_"+strconv.Itoa(y)]

			num := squareMax(point, points, sec)
			if num >= r {
				r = num
			}
		}
	}

	// fmt.Println(points)

	return r
}

func squareMax(point point, points map[string]point, sec sec) int {
	// 如果值不为1
	if point.value != 1 {
		return 0
	}

	r := 0

	for i := 0; i <= int(math.Min(float64(sec.y_max-point.y), float64(sec.x_max-point.x))); i++ {
		flag := true

		for t := 0; t < i; t++ {
			if points[strconv.Itoa(point.x+t)+"_"+strconv.Itoa(point.y)].value != 1 ||
				points[strconv.Itoa(point.x)+"_"+strconv.Itoa(point.y+t)].value != 1 ||
				points[strconv.Itoa(point.x+t)+"_"+strconv.Itoa(point.y+t)].value != 1 {
				flag = false
				break
			}
		}

		if flag {
			r += 1
		}
	}

	return r * r
}

func convertPoint(matrix [][]byte) map[string]point {
	var points map[string]point

	points = make(map[string]point)

	for i := 0; i < len(matrix); i++ {
		for j := 0; j < len(matrix[i]); j++ {
			points[strconv.Itoa(i+1)+"_"+strconv.Itoa(j+1)] = point{
				x:     i + 1,
				y:     j + 1,
				value: int(matrix[i][j]),
			}
		}
	}

	return points
}

func main() {
	fmt.Println(maximalSquare([][]byte{
		{1, 0, 1, 0, 0}, {1, 0, 1, 1, 1}, {1, 1, 1, 1, 1}, {1, 0, 0, 1, 0},
	}))
	fmt.Println(maximalSquare([][]byte{
		{0, 1}, {1, 0},
	}))
	fmt.Println(maximalSquare([][]byte{
		{0},
	}))
}

```