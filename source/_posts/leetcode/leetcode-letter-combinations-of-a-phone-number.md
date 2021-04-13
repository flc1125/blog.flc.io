----
title: LeetCode：电话号码的字母组合
date: 2021-03-31 23:05:13
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![](https://s.flc.io/2021-03-31-23-09-24.png)

<!-- more -->

**示例 1：**

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例 2：**

```
输入：digits = ""
输出：[]

```
**示例 3：**

```
输入：digits = "2"
输出：["a","b","c"]
```

**提示：**

- `0 <= digits.length <= 4`
- `digits[i]` 是范围 `['2', '9']` 的一个数字。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number

## 思路

将数字转换为具体的字母集合后，就会发现其实就是一个 **笛卡尔积** 算法。生活中场景的示例如：**衣服款式**

- 颜色：红、黄、蓝
- 尺寸：L、XL、XXL
- 款式：修身、短款

## 代码

### Go

```go
package main

import "fmt"
import "strings"

var nums map[string][]string = map[string][]string{
	"2": {"a", "b", "c"},
	"3": {"d", "e", "f"},
	"4": {"g", "h", "i"},
	"5": {"j", "k", "l"},
	"6": {"m", "n", "o"},
	"7": {"p", "q", "r", "s"},
	"8": {"t", "u", "v"},
	"9": {"w", "x", "y", "z"},
}

func letterCombinations(digits string) []string {
	ds := strings.Split(digits, "")
	var prepare [][]string

	// 预处理转换
	for _, d := range ds {
		temp := []string{}

		for _, v := range nums[d] {
			temp = append(temp, v)
		}

		prepare = append(prepare, temp)
	}

	// 验证
	if len(prepare) == 0 {
		return []string{}
	}

	// 笛卡尔积
	var result []string
	result = []string{""} // 默认值，用于循环

	for i := 0; i < len(prepare); i++ {
		result = crossJoin(result, prepare[i])
	}

	return result
}

func crossJoin(strs1 []string, strs2 []string) []string {
	var result []string

	for _, v1 := range strs1 {
		for _, v2 := range strs2 {
			result = append(result, (v1 + v2))
		}
	}

	return result
}

func main() {
	fmt.Println(letterCombinations("234"))
}
```