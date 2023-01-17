----
title: LeetCode：买卖股票的最佳时机
date: 2021-5-7 12:39:40
categories: LeetCode
tags: 
- Go
- LeetCode
- 动态规划
toc: true
----

## 题目

给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

**示例 1：**

```
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

<!-- more -->

**示例 2：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。
```

**提示：**

- `1 <= prices.length <= 10^5`
- `0 <= prices[i] <= 10^4`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock

## 代码

### Go

```go
package main

import (
	"fmt"
)

func maxProfit(prices []int) int {
	var afterMax, m int

	c := len(prices)
	for i := c - 1; i > 0; i-- {
		afterMax = max(afterMax, prices[i])

		prev := prices[

		m = max(afterMax-prev, m)
	}

	return m
}

func max(x, y int) int {
	if x > y {
		return x
	}

	return y
}

func main() {
	fmt.Println(maxProfit([]int{7, 1, 5, 3, 6, 4}))
	fmt.Println(maxProfit([]int{7, 6, 4, 3, 1}))
}
```