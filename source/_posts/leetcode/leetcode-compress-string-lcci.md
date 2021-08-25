----
title: LeetCode：字符串压缩
date: 2021-06-28 23:02:55
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

字符串压缩。利用字符重复出现的次数，编写一种方法，实现基本的字符串压缩功能。比如，字符串 `aabcccccaaa` 会变为 `a2b1c5a3`。若“压缩”后的字符串没有变短，则返回原先的字符串。你可以假设字符串中只包含大小写英文字母（a至z）。

**示例1:**

```
输入："aabcccccaaa"
输出："a2b1c5a3"
```

<!-- more -->

**示例2:**

```
输入："abbccd"
输出："abbccd"
解释："abbccd"压缩后为"a1b2c2d1"，比原字符串长度更长。
```

**提示：**

- 字符串长度在[0, 50000]范围内。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/compress-string-lcci

## 代码

### Go

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

func compressString(S string) string {
	SS := strings.Split(S, "")
	c := len(SS)

	var result []string

	for i := 0; i < len(SS); i++ {
		n, v := 1, SS[i]
		for {
			if i+1 == c {
				break
			}

			if (SS[i+1]) == SS[i] {
				n++
				i++
				continue
			}

			break
		}

		result = append(result, v+strconv.Itoa(n))
	}

	r := strings.Join(result, "")

	if len(r) < c {
		return r
	}

	return S
}

func main() {
	fmt.Println(compressString("aabcccccaaa"))
}
```