----
title: LeetCode：实现 strStr()
date: 2021-04-20 23:12:15
categories: LeetCode
tags: 
- Go
- LeetCode
toc: true
----

## 题目

实现 `strStr()` 函数。

给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串出现的第一个位置（下标从 `0` 开始）。如果不存在，则返回  `-1` 。

**说明：**

当 `needle` 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 `needle` 是空字符串时我们应当返回 `0` 。这与 C 语言的 `strstr()` 以及 Java 的 `indexOf()` 定义相符。

**示例 1：**

```
输入：haystack = "hello", needle = "ll"
输出：2
```

<!-- more -->

**示例 2：**

```
输入：haystack = "aaaaa", needle = "bba"
输出：-1
```

**示例 3：**

```
输入：haystack = "", needle = ""
输出：0
```

**提示：**

- `0 <= haystack.length, needle.length <= 5 * 10^4`
- `haystack` 和 `needle` 仅由小写英文字符组成

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/implement-strstr

## 代码

### Go

```go
package main

import (
	"fmt"
	"strings"
)

func strStr(haystack string, needle string) int {
	return strings.Index(haystack, needle)
}

func main() {
	fmt.Println(strStr("abcdefg", "cde"))
}
```