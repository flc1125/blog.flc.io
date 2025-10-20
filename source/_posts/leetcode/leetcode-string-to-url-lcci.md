----
title: LeetCode：URL化
date: 2021-06-08 21:42:45
categories: 编程
tags: 
- Go
- LeetCode
toc: true
----

## 题目

URL 化。编写一种方法，将字符串中的空格全部替换为 `%20` 。假定该字符串尾部有足够的空间存放新增字符，并且知道字符串的“真实”长度。（注：用Java实现的话，请使用字符数组实现，以便直接在数组上操作。）

**示例 1：**

```
输入："Mr John Smith    ", 13
输出："Mr%20John%20Smith"
```

<!-- more -->

**示例 2：**

```
输入："               ", 5
输出："%20%20%20%20%20"
```

**提示：**

- 字符串长度在 [0, 500000] 范围内。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/string-to-url-lcci

## 代码

### Go

```go
package main

import (
	"fmt"
	"strings"
)

func replaceSpaces(S string, length int) string {
	S = S[:length]

	return strings.Replace(S, " ", "%20", -1)
}

func main() {
	fmt.Println(replaceSpaces("Mr John Smith    ", 13))
	fmt.Println(replaceSpaces("         ", 5))
}
```