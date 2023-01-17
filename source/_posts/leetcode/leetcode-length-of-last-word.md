----
title: LeetCode：最后一个单词的长度
date: 2021-5-7 12:49:49
categories: LeetCode
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个字符串 `s`，由若干单词组成，单词之间用空格隔开。返回字符串中最后一个单词的长度。如果不存在最后一个单词，请返回 0 。

单词 是指仅由字母组成、不包含任何空格字符的最大子字符串。

**示例 1：**

```
输入：s = "Hello World"
输出：5
```

<!-- more -->

**示例 2：**

```
输入：s = " "
输出：0
```

**提示：**

- `1 <= s.length <= 10^4`
- `s` 仅有英文字母和空格 `' '` 组成

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/length-of-last-word

## 代码

### Go

```go
package main

import (
	"fmt"
	"strings"
)

func lengthOfLastWord(s string) int {
	ss := strings.Split(s, " ")

	c := len(ss)

	for i := c - 1; i >= 0; i-- {
		if ss[i] != "" {
			return len(ss[i])
		}
	}

	return 0
}

func main() {
	fmt.Println(lengthOfLastWord("Hello World"))
	fmt.Println(lengthOfLastWord("a "))
	fmt.Println(lengthOfLastWord(" "))
}
```