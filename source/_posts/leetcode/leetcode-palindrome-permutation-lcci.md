----
title: LeetCode：回文排列
date: 2021-06-09 23:56:32
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给定一个字符串，编写一个函数判定其是否为某个回文串的排列之一。

回文串是指正反两个方向都一样的单词或短语。排列是指字母的重新排列。

回文串不一定是字典当中的单词。

**示例1：**

```
输入："tactcoa"
输出：true（排列有"tacocat"、"atcocta"，等等）
```

<!-- more -->

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/palindrome-permutation-lcci

## 代码

### Go

```go
package main

import (
	"fmt"
	"sort"
	"strings"
)

func canPermutePalindrome(s string) bool {
	ss := strings.Split(s, "")
	sort.Strings(ss)
	c := len(ss)

	var singlyes []string

	for i := 0; i < c; i++ {
		if i < c-1 && ss[i] == ss[i+1] {
			i++
		} else {
			singlyes = append(singlyes, ss[i])
		}
	}

	return len(singlyes) <= 1
}

func main() {
	fmt.Println(canPermutePalindrome("tactcoa"))
}
```