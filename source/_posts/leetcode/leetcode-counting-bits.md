----
title: LeetCode：比特位计数
date: 2021-03-04 00:12:35
categories: LeetCode
tags: 
- Go
toc: true
----

## 题目

给定一个非负整数 `num`。对于 `0 ≤ i ≤ num` 范围中的每个数字 `i` ，计算其二进制数中的 1 的数目并将它们作为数组返回。

**示例 1:**

```
输入: 2
输出: [0,1,1]
```

**示例 2:**

```
输入: 5
输出: [0,1,1,2,1,2]
```

<!-- more -->

**进阶:**

- 给出时间复杂度为 `O(n*sizeof(integer))` 的解答非常容易。但你可以在线性时间 `O(n)` 内用一趟扫描做到吗？
- 要求算法的空间复杂度为 `O(n)`。
- 你能进一步完善解法吗？要求在 C++ 或任何其他语言中不使用任何内置函数（如 `C++` 中的 `__builtin_popcount`）来执行此操作。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/counting-bits

## 思路

![](https://s.flc.io/2021-03-04-00-16-11.png)

如图：`6=4+2`；`15=8+4+2+1`……

仔细看会发现，如：`1、2、4、8……`等均只有一个 `1`，而有多少个这样的数，即有多少个1。

而 `1、2、4、8` 均为 2 的 X 次方。

## 代码

### Go

```go
package main

import "fmt"

func countBits(num int) []int {
	var result []int

	for i := 0; i <= num; i++ {
		result = append(result, countBit(i))
	}

	return result
}

func countBit(num int) int {
	j := 0
	i := 0

	for {
		if (1 << i) > num {
			break
		}

		if (1<<i)&num > 0 {
			j++
		}

		i++
	}

	return j
}

func main() {
	fmt.Println(countBit(5))
	fmt.Println(countBits(5))
	fmt.Println(countBits(2))
	fmt.Println(countBits(50))
}

```