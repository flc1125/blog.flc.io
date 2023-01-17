----
title: LeetCode：搜索插入位置（二分查找）
date: 2021-04-26 23:15:25
categories: LeetCode
tags: 
- Go
- LeetCode
- 二分查找
toc: true
----

## 题目

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

**示例 1:**

```
输入: [1,3,5,6], 5
输出: 2
```

<!-- more -->

**示例 2:**

```
输入: [1,3,5,6], 2
输出: 1
```

**示例 3:**

```
输入: [1,3,5,6], 7
输出: 4
```

**示例 4:**

```
输入: [1,3,5,6], 0
输出: 0
```

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/search-insert-position

## 代码

### Go

> 使用 **二分法查找** 方式进行获取，主要是学习该方法。

```go
package main

import "fmt"

// 使用二分查找方式
func searchInsert(nums []int, target int) int {
	left, right := 0, len(nums)-1

	for {
		if left > right {
			break
		}

		// 获取中间的值
		mid := int((left + right) / 2)
		midVal := nums[mid]

		if midVal == target {
			return mid
		} else if midVal > target {
			right = mid - 1
		} else if midVal < target {
			left = mid + 1
		}
	}

	return left
}

func main() {
	fmt.Println(searchInsert([]int{1, 3, 5, 6}, 5))
	fmt.Println(searchInsert([]int{1, 3, 5, 6}, 2))
}
```

## 总结

**二分查找方法的特性：**

- 元素需要有序
- 依靠二分法，能减少大量运算，以提高性能

## 其他

- 相关题库：https://leetcode-cn.com/tag/binary-search/