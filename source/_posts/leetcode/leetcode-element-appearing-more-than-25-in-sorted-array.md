----
title: LeetCode：有序数组中出现次数超过25%的元素
date: 2021-4-8 23:02:08
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个非递减的 **有序** 整数数组，已知这个数组中恰好有一个整数，它的出现次数超过数组元素总数的 25%。

请你找到并返回这个整数

**示例：**

```
输入：arr = [1,2,2,6,6,6,6,7,10]
输出：6
```

<!-- more -->

**提示：**

- `1 <= arr.length <= 10^4`
- `0 <= arr[i] <= 10^5`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/element-appearing-more-than-25-in-sorted-array

## 代码

### Go

```go
package main

import "fmt"

func findSpecialInteger(arr []int) int {
	var group map[int]int
	group = make(map[int]int)

	count := len(arr)

	for _, v := range arr {
		group[v] += 1

		if group[v]*4 > count {
			return v
		}
	}

	panic("error")
}

func main() {
	fmt.Println(findSpecialInteger([]int{1, 2, 2, 6, 6, 6, 6, 7, 10}))
}
```