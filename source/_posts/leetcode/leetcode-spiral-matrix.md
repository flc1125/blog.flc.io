----
title: LeetCode：螺旋矩阵
date: 2021-3-15 20:20:15
categories: LeetCode
tags: 
- Go
toc: true
----

## 题目

给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

**示例 1：**

![](https://s.flc.io/2021-03-15-20-23-05.jpg)

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

<!-- more -->

**示例 2：**

![](https://s.flc.io/2021-03-15-20-23-20.jpg)

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`
- `-100 <= matrix[i][j] <= 100`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/spiral-matrix

## 思路

![](https://s.flc.io/2021-03-15-20-31-55.png)

**如图顺序：**

- 第一行正常顺序
- 非首尾行取最后一个元素
- 尾行倒序
- 在非首尾行的首列倒序
- 剩下黄色区域复用以上逻辑（递归）

## 代码

### Go

```go
package main

import "fmt"

func spiralOrder(matrix [][]int) []int {
	var result []int
	var cl []int   // 非首尾行的首字母集合
	var cc [][]int // 非首尾行中间字母多维集合

	for i, m := range matrix {
		matrix_len := len(matrix)

		if i == 0 {
			result = append(result, m...)
		}

		if i > 0 && i < matrix_len-1 {
			m_len := len(m)

			result = append(result, m[m_len-1])

			if matrix_len >= 3 && m_len >= 2 {
				cl = append([]int{m[0]}, cl...)
			}

			if m_len >= 3 {

				cc = append(cc, m[1:m_len-1])
			}
		}

		if matrix_len > 1 && i == matrix_len-1 {
			result = append(result, reverseInts(m)...)
		}
	}

	result = append(result, cl...)
	if len(cc) > 0 {
		result = append(result, spiralOrder(cc)...)
	}

	return result
}

// 反转
func reverseInts(m []int) []int {
	var r []int

	for i := range m {
		r = append(r, m[len(m)-1-i])
	}

	return r
}

func main() {
	fmt.Println(reverseInts([]int{1, 2, 3}))

	fmt.Println(spiralOrder([][]int{
		{1, 2, 3}, {4, 5, 6}, {7, 8, 9},
	}))

	fmt.Println(spiralOrder([][]int{
		{2, 5}, {8, 4}, {0, -1},
	}))
}

```