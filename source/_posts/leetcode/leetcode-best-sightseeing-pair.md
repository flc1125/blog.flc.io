----
title: LeetCode：最佳观光组合
date: 2021-03-11 00:14:51
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个正整数数组 `values`，其中 `values[i]` 表示第 `i` 个观光景点的评分，并且两个景点 `i` 和 `j` 之间的 **距离** 为 `j - i`。

一对景点（`i < j`）组成的观光组合的得分为 `values[i] + values[j] + i - j` ，也就是景点的评分之和 **减去** 它们两者之间的距离。

返回一对观光景点能取得的最高分。

<!-- more -->

**示例 1：**

```
输入：values = [8,1,5,2,6]
输出：11
解释：i = 0, j = 2, values[i] + values[j] + i - j = 8 + 5 + 0 - 2 = 11
```

**示例 2：**

```
输入：values = [1,2]
输出：2
```

**提示：**

- `2 <= values.length <= 5 * 10^4`
- `1 <= values[i] <= 1000`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/best-sightseeing-pair

## 代码

### Go

- 代码一：实际在大数值时候，会超时

    ```go
    package main

    import (
        "fmt"
    )

    func maxScoreSightseeingPair(values []int) int {
        var score float64

        for i := 0; i < len(values); i++ {
            for j := i + 1; j < len(values); j++ {
                score = math.Max(score, float64(values[i]+values[j]+i-j))
            }
        }

        return int(score)
    }

    func main() {
        fmt.Println(maxScoreSightseeingPair([]int{8, 1, 5, 2, 6}))
        fmt.Println(maxScoreSightseeingPair([]int{2, 2, 1}))
        fmt.Println(maxScoreSightseeingPair([]int{1, 1, 1}))
    }
    ```

- 代码二：复用循环，减少IO

    ```go
    package main

	import (
		"fmt"
	)

	func max(x int, y int) int {
		if x > y {
			return x
		}

		return y
	}

	func maxScoreSightseeingPair(values []int) int {
		var score int
		vi := values[0] + 0

		for j := 1; j < len(values); j++ {
			score = max(score, vi+values[j]-j)
			vi = max(vi, values[j]+j)
		}

		return score
	}

	func main() {
		fmt.Println(maxScoreSightseeingPair([]int{8, 1, 5, 2, 6}))
		fmt.Println(maxScoreSightseeingPair([]int{2, 2, 1}))
		fmt.Println(maxScoreSightseeingPair([]int{1, 1, 1}))
		fmt.Println(maxScoreSightseeingPair([]int{
			402,
			224,
			922,
			720,
			323,
			714,
			129,
			303,
			296,
			50,
			272,
			14,
			468,
			485,
			829,
			820,
			636,
			787,
			16,
			975,
			579,
			186,
			76,
			830,
			296,
			649,
			701,
			670,
			52,
			82,
			885,
			452,
			175,
			564,
			345,
			729,
			433,
			797,
		}))
	}

    ```