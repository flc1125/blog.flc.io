----
title: LeetCode：回文数
date: 2021-05-14 23:55:48
categories: LeetCode
tags: 
- Go
- LeetCode
toc: true
----

## 题目


给你一个整数 `x` ，如果 `x` 是一个回文整数，返回 `true` ；否则，返回 `false` 。

回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。例如，`121` 是回文，而 `123` 不是。

**示例 1：**

```
输入：x = 121
输出：true
```

<!-- more -->

**示例 2：**

```
输入：x = -121
输出：false
解释：从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```

**示例 3：**

```
输入：x = 10
输出：false
解释：从右向左读, 为 01 。因此它不是一个回文数。
```

**示例 4：**

```
输入：x = -101
输出：false
```

**提示：**

- `-2^31 <= x <= 2^31 - 1`

**进阶：** 你能不将整数转为字符串来解决这个问题吗？

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/palindrome-number

## 代码

### Go

```go
package main

import (
	"fmt"
	"strconv"
)

func isPalindrome(x int) bool {
	str := strconv.Itoa(x)
	first, end := 0, len(str)-1

	for first <= end {
		if str[first] != str[end] {
			return false
		}

		first++
		end--
	}

	return true
}

func main() {
	fmt.Println(isPalindrome(121))
}
```