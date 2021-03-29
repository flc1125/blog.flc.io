----
title: LeetCode：统计最大组的数目
date: 2021-03-23 23:27:17
categories: 编程
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个整数 `n` 。请你先求出从 `1` 到 `n` 的每个整数 10 进制表示下的数位和（每一位上的数字相加），然后把数位和相等的数字放到同一个组中。

请你统计每个组中的数字数目，并返回数字数目并列最多的组有多少个。

**示例 1：**

```
输入：n = 13
输出：4
解释：总共有 9 个组，将 1 到 13 按数位求和后这些组分别是：
[1,10]，[2,11]，[3,12]，[4,13]，[5]，[6]，[7]，[8]，[9]。总共有 4 个组拥有的数字并列最多。
```

<!-- more -->

**示例 2：**

```
输入：n = 2
输出：2
解释：总共有 2 个大小为 1 的组 [1]，[2]。
```

**示例 3：**

```
输入：n = 15
输出：6
```

**示例 4：**

```
输入：n = 24
输出：5
```

**提示：**

- `1 <= n <= 10^4`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/count-largest-group

## 代码

### Go

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

func countLargestGroup(n int) int {
	var results map[int][]int
	var maxCount, count int = 0, 0

	results = make(map[int][]int)

	for i := 1; i <= n; i++ {
		key := sumInt(i)

		results[key] = append(results[key], i)

		maxCount = maxInt(maxCount, len(results[key]))
	}

	for _, v := range results {
		if len(v) == maxCount {
			count++
		}
	}

	return count
}

func sumInt(num int) int {
	nums, r := strings.Split(strconv.Itoa(num), ""), 0

	for _, v := range nums {
		vn, _ := strconv.Atoi(v)

		r += vn
	}

	return r
}

func maxInt(a int, b int) int {
	if a > b {
		return a
	}

	return b
}

func main() {
	fmt.Println(countLargestGroup(13))
	fmt.Println(countLargestGroup(2))
	fmt.Println(countLargestGroup(15))
	fmt.Println(countLargestGroup(24))
	fmt.Println(countLargestGroup(46))
}
```

> 题目有点绕，不提交的话，题目都不明白啥意思~~~