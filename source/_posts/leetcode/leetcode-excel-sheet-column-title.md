----
title: LeetCode：Excel 表列名称
date: 2021-07-01 00:08:47
categories: LeetCode
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个整数 `columnNumber` ，返回它在 Excel 表中相对应的列名称。

例如：

A -> 1
B -> 2
C -> 3
...
Z -> 26
AA -> 27
AB -> 28 
...

<!-- more -->

**示例 1：**

```
输入：columnNumber = 1
输出："A"
```

**示例 2：**

```
输入：columnNumber = 28
输出："AB"
```

**示例 3：**

```
输入：columnNumber = 701
输出："ZY"
```

**示例 4：**

```
输入：columnNumber = 2147483647
输出："FXSHRXW"
```

**提示：**

- `1 <= columnNumber <= 2^31 - 1`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/excel-sheet-column-title

## 代码

### Go

```go
package main

import (
	"fmt"
	"strings"
)

func convertToTitle(columnNumber int) string {
	var strs []string

	for columnNumber > 0 {
		columnNumber -= 1

		strs = append([]string{string(64 + columnNumber%26 + 1)}, strs...)

		columnNumber /= 26
	}

	return strings.Join(strs, "")
}

func main() {
	fmt.Println(convertToTitle(1))
	fmt.Println(convertToTitle(26))
	fmt.Println(convertToTitle(28))
	fmt.Println(convertToTitle(30))
	fmt.Println(convertToTitle(701))
}
```