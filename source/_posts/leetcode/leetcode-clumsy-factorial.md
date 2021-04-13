----
title: LeetCode：笨阶乘
date: 2021-04-02 00:17:39
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

通常，正整数 n 的阶乘是所有小于或等于 n 的正整数的乘积。例如，`factorial(10) = 10 * 9 * 8 * 7 * 6 * 5 * 4 * 3 * 2 * 1`。

相反，我们设计了一个笨阶乘 `clumsy`：在整数的递减序列中，我们以一个固定顺序的操作符序列来依次替换原有的乘法操作符：乘法(*)，除法(/)，加法(+)和减法(-)。

例如，`clumsy(10) = 10 * 9 / 8 + 7 - 6 * 5 / 4 + 3 - 2 * 1`。然而，这些运算仍然使用通常的算术运算顺序：我们在任何加、减步骤之前执行所有的乘法和除法步骤，并且按从左到右处理乘法和除法步骤。

另外，我们使用的除法是地板除法（*floor division*），所以 `10 * 9 / 8` 等于 `11`。这保证结果是一个整数。

实现上面定义的笨函数：给定一个整数 `N`，它返回 `N` 的笨阶乘。

<!-- more -->

**示例 1：**

```
输入：4
输出：7
解释：7 = 4 * 3 / 2 + 1
```

**示例 2：**

```
输入：10
输出：12
解释：12 = 10 * 9 / 8 + 7 - 6 * 5 / 4 + 3 - 2 * 1
```

提示：

- `1 <= N <= 10000`
- `-2^31 <= answer <= 2^31 - 1`  （答案保证符合 32 位整数。）

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/clumsy-factorial

## 思路

数字规律，每三个存在一个乘除组合运算+一个加号运算。基于此进行分组即可。

## 代码

### Go

```go
package main

import (
	"fmt"
	"math"
)

func clumsy(N int) int {
	prepare, r := prepare(N)

	for k, p := range prepare {
		if k == 0 {
			r += calc(p)
		} else {
			r -= calc(p)
		}
	}

	return r
}

func calc(p []int) int {
	l := len(p)
	r := p[0]

	if l >= 2 {
		r *= p[1]
	}

	if l >= 3 {
		r = int(math.Floor(float64(r) / float64(p[2])))
	}

	return r
}

func prepare(N int) ([][]int, int) {
	j, r := 1, 0

	var prepare [][]int
	var temp []int

	for i := N; i >= 1; i-- {
		if j%4 == 0 {
			r += i
			j++
			continue
		}

		if j%4 == 1 {
			temp = []int{}
		}

		temp = append(temp, i)

		if i == 1 || j%4 == 3 {
			prepare = append(prepare, temp)
		}

		j++
	}

	return prepare, r
}

func main() {
	// fmt.Println(calc([]int{4, 3, 2, 1}))
	fmt.Println(clumsy(4))
	fmt.Println(clumsy(8))
	fmt.Println(clumsy(10))
	// fmt.Println(clumsy(100))
}
```