----
title: LeetCode：全排列
date: 2021-06-29 23:50:37
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
- 回溯
toc: true
----

## 题目

给定一个不含重复数字的数组 `nums` ，返回其 **所有可能的全排列** 。你可以 **按任意顺序** 返回答案。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

<!-- more -->

**示例 2：**

```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**示例 3：**

```
输入：nums = [1]
输出：[[1]]
```

**提示：**

- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有整数 **互不相同**

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/permutations

## 代码

### Go

```go
package main

import "fmt"

func permute(nums []int) [][]int {
	var result [][]int
	c := len(nums)

	for i := 0; i < c; i++ {
		dst := make([]int, c)
		copy(dst, nums)

		t := append(dst[:i], dst[(i+1):]...)

		var tt []int
		tt = append(tt, nums[i])

		ttt := permute(t)

		if len(ttt) > 0 {
			for _, v := range permute(t) {
				result = append(result, append(tt, v...))
			}
		} else {
			result = append(result, tt)
		}
	}

	return result
}

func main() {
	fmt.Println(permute([]int{1}))
	fmt.Println(permute([]int{1, 2}))
	fmt.Println(permute([]int{1, 2, 3}))
	fmt.Println(permute([]int{1, 2, 3, 4}))
}
```