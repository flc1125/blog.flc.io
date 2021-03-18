----
title: LeetCode：HTML 实体解析器
date: 2021-03-18 23:40:11
categories: 编程
tags: 
- Go
- LeetCode
toc: true
----

## 题目

「HTML 实体解析器」 是一种特殊的解析器，它将 HTML 代码作为输入，并用字符本身替换掉所有这些特殊的字符实体。

HTML 里这些特殊字符和它们对应的字符实体包括：

- 双引号：字符实体为 `&quot;` ，对应的字符是 `"` 。
- 单引号：字符实体为 `&apos;` ，对应的字符是 `'` 。
- 与符号：字符实体为 `&amp;` ，对应对的字符是 `&` 。
- 大于号：字符实体为 `&gt;` ，对应的字符是 `>` 。
- 小于号：字符实体为 `&lt;` ，对应的字符是 `<` 。
- 斜线号：字符实体为 `&frasl;` ，对应的字符是 `/` 。

给你输入字符串 `text` ，请你实现一个 HTML 实体解析器，返回解析器解析后的结果。

<!-- more -->

**示例 1：**

```
输入：text = "&amp; is an HTML entity but &ambassador; is not."
输出："& is an HTML entity but &ambassador; is not."
解释：解析器把字符实体 &amp; 用 & 替换
```

**示例 2：**

```
输入：text = "and I quote: &quot;...&quot;"
输出："and I quote: \"...\""
```

**示例 3：**

```
输入：text = "Stay home! Practice on Leetcode :)"
输出："Stay home! Practice on Leetcode :)"
```

**示例 4：**

```
输入：text = "x &gt; y &amp;&amp; x &lt; y is always false"
输出："x > y && x < y is always false"
```

**示例 5：**

```
输入：text = "leetcode.com&frasl;problemset&frasl;all"
输出："leetcode.com/problemset/all"
```

**提示：**

- `1 <= text.length <= 10^5`
- 字符串可能包含 256 个ASCII 字符中的任意字符。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/html-entity-parser

## 思路

一开始以为 N 个替换就好，后来发现 `&amp;gt;` 会多次转义得到结果 `>`，但实际期望是 `&gt;`。所以……正则搞起……

## 代码

### Go

```go
package main

import (
	"fmt"
	"regexp"
)

var parsers = map[string]string{
	"&quot;":  "\"",
	"&apos;":  "'",
	"&amp;":   "&",
	"&gt;":    ">",
	"&lt;":    "<",
	"&frasl;": "/",
}

func entityParser(text string) string {
	re := regexp.MustCompile(`(&quot;|&apos;|&amp;|&gt;|&lt;|&frasl;)`)

	result := re.ReplaceAllStringFunc(text, func(s string) string {
		matchs := re.FindStringSubmatch(s)

		for k, v := range parsers {
			if k == matchs[1] {
				return v
			}
		}

		return ""
	})

	return result
}

func main() {
	fmt.Println(entityParser("&amp; is an HTML entity but &ambassador; is not."))
	fmt.Println(entityParser("&amp;gt;"))
}
```