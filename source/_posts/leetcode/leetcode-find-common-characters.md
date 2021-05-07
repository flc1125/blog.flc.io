----
title: LeetCode：查找常用字符
date: 2021-5-7 14:25:27
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给定仅有小写字母组成的字符串数组 `A`，返回列表中的每个字符串中都显示的全部字符（**包括重复字符**）组成的列表。例如，如果一个字符在每个字符串中出现 3 次，但不是 4 次，则需要在最终答案中包含该字符 3 次。

你可以按任意顺序返回答案。

**示例 1：**

```
输入：["bella","label","roller"]
输出：["e","l","l"]
```

<!-- more -->

**示例 2：**

```
输入：["cool","lock","cook"]
输出：["c","o"]
```

**提示：**

- `1 <= A.length <= 100`
- `1 <= A[i].length <= 100`
- `A[i][j]` 是小写字母

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/find-common-characters

## 代码

### Go

```go
package main

import (
	"fmt"
	"strings"
)

func commonChars(A []string) []string {
	var r []string
	firsts := strings.Split(A[0], "")

	for _, s := range firsts {
		flag := true

		for i := 1; i < len(A); i++ {
			if strings.Index(A[i], s) == -1 {
				flag = false
				break
			} else {
				A[i] = strings.Replace(A[i], s, "", 1)
			}
		}

		if flag {
			r = append(r, s)
		}
	}

	return r
}

func main() {
	fmt.Println(commonChars([]string{"bella", "label", "roller"}))
	fmt.Println(commonChars([]string{"cool", "lock", "cook"}))
}
```