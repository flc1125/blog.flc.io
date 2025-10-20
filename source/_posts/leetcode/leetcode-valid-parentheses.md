----
title: LeetCode：有效的括号
date: 2021-04-28 23:18:21
updated: 2021-04-30 00:07:52
categories: 编程
tags: 
- Go
- LeetCode
- 堆栈
toc: true
----

## 题目

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

有效字符串需满足：

- 左括号必须用相同类型的右括号闭合。
- 左括号必须以正确的顺序闭合。

**示例 1：**

```
输入：s = "()"
输出：true
```

<!-- more -->

**示例 2：**

```
输入：s = "()[]{}"
输出：true
```

**示例 3：**

```
输入：s = "(]"
输出：false
```

**示例 4：**

```
输入：s = "([)]"
输出：false
```

`示例 5：`

```
输入：s = "{[]}"
输出：true
```

**提示：**

- `1 <= s.length <= 10^4`
- `s` 仅由括号 `'()[]{}'` 组成

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/valid-parentheses

## 代码

### Go

**普通写法：**

```go
package main

import (
	"fmt"
	"strings"
)

func isValid(s string) bool {
	for {
		o := s

		s = strings.Replace(s, "()", "", -1)
		s = strings.Replace(s, "[]", "", -1)
		s = strings.Replace(s, "{}", "", -1)

		if o == s {
			break
		}
	}

	return s == ""
}

func main() {
	fmt.Println(isValid("()"))
	fmt.Println(isValid("()[]{}"))
	fmt.Println(isValid("{[]}"))
	fmt.Println(isValid("[{}]"))
}
```

**堆栈写法：**

```go
package main

import (
	"fmt"
)

func isValid(s string) bool {
	stack := []string{}

	config := map[string]string{
		")": "(",
		"]": "[",
		"}": "{",
	}

	for i := 0; i < len(s); i++ {
		stri := string(s[i])

		// 如果遇到左括号，就开始堆栈
		if stri == "(" || stri == "[" || stri == "{" {
			stack = append(stack, stri)
		} else { // 否则从栈最右边进行比较和对比
			if len(stack) == 0 || stack[len(stack)-1] != config[stri] {
				return false
			}

			stack = stack[:len(stack)-1]
		}

	}

	return len(stack) == 0
}

func main() {
	fmt.Println(isValid("()"))
	fmt.Println(isValid("()[]{}"))
	fmt.Println(isValid("{[]}"))
	fmt.Println(isValid("[{}]"))
}
```