----
title: LeetCode：平方数之和
date: 2021-03-25 23:06:38
categories: 编程
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给定一个非负整数 `c` ，你要判断是否存在两个整数 `a` 和 `b`，使得 `a2 + b2 = c` 。

**示例 1：**

```
输入：c = 5
输出：true
解释：1 * 1 + 2 * 2 = 5
```

<!-- more -->

**示例 2：**

```
输入：c = 3
输出：false
```

**示例 3：**

```
输入：c = 4
输出：true
```

**示例 4：**

```
输入：c = 2
输出：true
```

**示例 5：**

```
输入：c = 1
输出：true
```

**提示：**

- `0 <= c <= 231 - 1`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/sum-of-square-numbers

## 代码

```go
package main

import (
	"fmt"
	"math"
)

func judgeSquareSum(c int) bool {
	for i := 0; i*i <= c; i++ {
		j := math.Sqrt(float64(c) - math.Pow(float64(i), 2))

		if isInt(j) {
			return true
		}
	}

	return false
}

func isInt(num float64) bool {
	return num == math.Trunc(num)
}

func main() {
	// fmt.Println(judgeSquareSum(5))
	// fmt.Println(judgeSquareSum(10000))
	// fmt.Println(judgeSquareSum(3))
	fmt.Println(judgeSquareSum(999999999))
}
```

> 关键的是 `i*i <= c`，能减少大量的循环（内存、执行时间）