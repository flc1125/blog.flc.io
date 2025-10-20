----
title: LeetCode：最大数
date: 2021-04-12 23:33:58
categories: 编程
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给定一组非负整数 `nums`，重新排列每个数的顺序（每个数不可拆分）使之组成一个最大的整数。

**注意：**输出结果可能非常大，所以你需要返回一个字符串而不是整数。

**示例 1：**

```
输入：nums = [10,2]
输出："210"
```

<!-- more -->

**示例 2：**

```
输入：nums = [3,30,34,5,9]
输出："9534330"
```

**示例 3：**

```
输入：nums = [1]
输出："1"
```

**示例 4：**

```
输入：nums = [10]
输出："10"
```

**提示：**

- 1 <= nums.length <= 100
- 0 <= nums[i] <= 10^9

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/largest-number

## 思路

`[10, 2]` 可组合为 `210` 和 `102`，而 `210 > 102`。

## 代码

### Go

```go
package main

import (
	"fmt"
	"sort"
	"strconv"
	"strings"
)

func largestNumber(nums []int) string {
	// 排序
	sort.Slice(nums, func(i, j int) bool {
		iStr := strconv.Itoa(nums[i])
		jStr := strconv.Itoa(nums[j])

		oneStr, _ := strconv.Atoi(iStr + jStr)
		twoStr, _ := strconv.Atoi(jStr + iStr)

		return oneStr > twoStr
	})

	// 拼接
	var r string
	for _, num := range nums {
		r += strconv.Itoa(num)
	}

	r = strings.TrimLeft(r, "0")

	if r == "" {
		return "0"
	}

	return r
}

func main() {
	fmt.Println(largestNumber([]int{10, 2}))
	fmt.Println(largestNumber([]int{3, 30, 34, 5, 9}))
	fmt.Println(largestNumber([]int{3, 30, 34}))
	fmt.Println(largestNumber([]int{8308, 8308, 830}))
	fmt.Println(largestNumber([]int{111311, 1113}))
	fmt.Println(largestNumber([]int{34323, 3432}))
	fmt.Println(largestNumber([]int{0, 0}))
}
```