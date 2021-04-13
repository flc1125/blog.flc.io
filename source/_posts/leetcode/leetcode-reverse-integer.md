----
title: LeetCode：整数反转
date: 2021-03-27 01:02:54
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个 32 位的有符号整数 `x` ，返回将 `x` 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 `[−2^31,  2^31 − 1]` ，就返回 0。

**假设环境不允许存储 64 位整数（有符号或无符号）。**

**示例 1：**

```
输入：x = 123
输出：321
```

<!-- more -->

**示例 2：**

```
输入：x = -123
输出：-321
```

**示例 3：**

```
输入：x = 120
输出：21
```

**示例 4：**

```
输入：x = 0
输出：0
```

**提示：**

- `-2^31 <= x <= 2^31 - 1`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/reverse-integer

## 代码

### Go

```go
package main

import "fmt"
import "math"
import "strconv"

func reverse(x int) int {
	xString := strconv.Itoa(int(math.Abs(float64(x))))
	var r string

	for i := len(xString) - 1; i >= 0; i-- {
		r += xString[i : i+1]
	}

	result, _ := strconv.Atoi(r)

	// 判定是否为负数
	pre := 1
	if x < 0 {
		pre = -1
	}

	// 判定是否范围内−2^31,  2^31 − 1
	if float64(result) < math.Pow(-2, 31) || float64(result+1) > math.Pow(2, 31) {
		return 0
	}

	return result * pre
}

func main() {
	fmt.Println(reverse(0))
	fmt.Println(reverse(123))
	fmt.Println(reverse(-123))
	fmt.Println(reverse(120))
}
```