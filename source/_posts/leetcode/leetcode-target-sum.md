----
title: LeetCode：目标和
date: 2021-06-07 23:46:42
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个整数数组 `nums` 和一个整数 `target` 。

向数组中的每个整数前添加 `'+'` 或 `'-'` ，然后串联起所有整数，可以构造一个 表达式 ：

例如，`nums = [2, 1]` ，可以在 `2` 之前添加 `'+'` ，在 `1` 之前添加 `'-'` ，然后串联起来得到表达式 `"+2-1"` 。
返回可以通过上述方法构造的、运算结果等于 `target` 的不同 **表达式** 的数目。

<!-- more -->

**示例 1：**

```
输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```

**示例 2：**

```
输入：nums = [1], target = 1
输出：1
```

**提示：**

- `1 <= nums.length <= 20`
- `0 <= nums[i] <= 1000`
- `0 <= sum(nums[i]) <= 1000`
- `-1000 <= target <= 100`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/target-sum

## 思路

第一想到的就是笛卡尔积算法~

## 代码

### Go

```go
package main

import "fmt"

func findTargetSumWays(nums []int, target int) int {
	var result int
	c, groups := len(nums), []int{0}

	for i := 0; i <= c; i++ {
		var temp []int

		for _, group := range groups {
			if c == i {
				if group == target {
					result++
				}

				continue
			}

			temp = append(temp, group+nums[i])
			temp = append(temp, group-nums[i])
		}

		groups = temp
	}

	return result
}

func main() {
	fmt.Println(findTargetSumWays([]int{1, 1, 1, 1, 1}, 3))
	fmt.Println(findTargetSumWays([]int{1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1}, 0))
	fmt.Println(findTargetSumWays([]int{2, 107, 109, 113, 127, 131, 137, 3, 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 47, 53}, 33))
}
```

执行耗时：

```bash
➜  leetcode git:(master) ✗ time go run 494.go 
5
12870
0
go run 494.go  0.23s user 0.30s system 55% cpu 0.964 total
➜  leetcode git:(master) ✗ 
```

> 官方有点 🐶，这个耗时，居然不通过，通过用例：128/138