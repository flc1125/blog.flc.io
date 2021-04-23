----
title: LeetCode：有序数组中的单一元素
date: 2021-04-23 22:54:32
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给定一个只包含整数的有序数组，每个元素都会出现两次，唯有一个数只会出现一次，找出这个数。

**示例 1:**

```
输入: [1,1,2,3,3,4,4,8,8]
输出: 2
```

<!-- more -->

**示例 2:**

```
输入: [3,3,7,7,10,11,11]
输出: 10
```

**注意:** 您的方案应该在 `O(log n)` 时间复杂度和 `O(1)` 空间复杂度中运行。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/single-element-in-a-sorted-array

## 代码

### Go

```go
package main

import "fmt"

func singleNonDuplicate(nums []int) int {
	var ns map[int]int
	ns = make(map[int]int)
	for i := 0; i < len(nums); i++ {
		ns[nums[i]] += 1
	}

	for k, v := range ns {
		if v == 1 {
			return k
		}
	}

	panic("error")
}

func main() {
	fmt.Println(singleNonDuplicate([]int{1, 1, 2, 3, 3, 4, 4, 8, 8}))
}
```