----
title: LeetCode：判定字符是否唯一
date: 2021-06-08 21:28:49
categories: 编程
tags: 
- Go
- LeetCode
toc: true
----

## 题目

实现一个算法，确定一个字符串 `s` 的所有字符是否全都不同。

**示例 1：**

```
输入: s = "leetcode"
输出: false 
```

<!-- more -->

**示例 2：**

```
输入: s = "abc"
输出: true
```

**限制：**

- `0 <= len(s) <= 100`
- 如果你不使用额外的数据结构，会很加分。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/is-unique-lcci

## 代码

### Go

```go
package main

import (
	"fmt"
	"strings"
)

func isUnique(astr string) bool {
	for i := 0; i < len(astr); i++ {
		if strings.Index(astr[i+1:], string(astr[i])) >= 0 {
			return false
		}
	}

	return true
}

func main() {
	fmt.Println(isUnique("aaa"))
	fmt.Println(isUnique("abc"))
	fmt.Println(isUnique("abcabc"))
}
```