----
title: LeetCode：前 K 个高频元素
date: 2021-04-22 00:30:18
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。

**示例 1:**

```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```

**示例 2:**

```
输入: nums = [1], k = 1
输出: [1]
```

<!-- more -->

**提示：**

- `1 <= nums.length <= 10^5`
- `k` 的取值范围是 `[1, 数组中不相同的元素的个数]`
- 题目数据保证答案唯一，换句话说，数组中前 `k` 个高频元素的集合是唯一的
 

**进阶：**你所设计算法的时间复杂度 **必须** 优于 `O(n log n)` ，其中 `n` 是数组大小。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/top-k-frequent-elements

## 代码

### Go

```go
type item struct {
	v, c int
}

func topKFrequent(nums []int, k int) []int {
	var m map[int]int
	m = make(map[int]int)
	arr := []item{}

	for _, v := range nums {
		m[v] += 1
	}

	for v, c := range m {
		arr = append(arr, item{v, c})
	}

	sort.Slice(arr, func(i, j int) bool {
		return arr[i].c > arr[j].c
	})

	var r []int
	for i, v := range arr {
		if i <= k-1 {
			r = append(r, v.v)
		}
	}

	return r
}
```