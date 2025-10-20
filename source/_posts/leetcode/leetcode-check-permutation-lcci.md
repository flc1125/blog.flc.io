----
title: LeetCode：判定是否互为字符重排
date: 2021-06-08 21:35:45
categories: 编程
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给定两个字符串 `s1` 和 `s2`，请编写一个程序，确定其中一个字符串的字符重新排列后，能否变成另一个字符串。

**示例 1：**

```
输入: s1 = "abc", s2 = "bca"
输出: true 
```

<!-- more -->

**示例 2：**

```
输入: s1 = "abc", s2 = "bad"
输出: false
```

**说明：**

- `0 <= len(s1) <= 100`
- `0 <= len(s2) <= 100`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/check-permutation-lcci

## 代码

### Go

```go
package main

import (
	"fmt"
	"sort"
	"strings"
)

func CheckPermutation(s1 string, s2 string) bool {
	ss1, ss2 := strings.Split(s1, ""), strings.Split(s2, "")

	sort.Strings(ss1)
	sort.Strings(ss2)

	return strings.Join(ss1, "") == strings.Join(ss2, "")
}

func main() {
	fmt.Println(CheckPermutation("abc", "acb"))
}
```