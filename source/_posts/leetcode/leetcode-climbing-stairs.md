----
title: LeetCode：爬楼梯
date: 2021-03-12 23:55:21
categories: LeetCode
tags: 
- Go
toc: true
----

> 未完待续

## 题目

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意：**给定 n 是一个正整数。

<!-- more -->

**示例 1：**

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
```

**示例 2：**

```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/climbing-stairs

## 代码

### Go

- 代码一：大数据执行超时

    ```go
    package main

    import "fmt"

    func climbStairs(n int) int {
        var r int

        if n-2 > 0 {
            r += climbStairs(n - 2)
        }

        if n-1 > 0 {
            r += climbStairs(n - 1)
        }

        if n-2 == 0 || n-1 == 0 {
            return r + 1
        }

        return r
    }

    func main() {
        fmt.Println(climbStairs(1))
        fmt.Println(climbStairs(2))
        fmt.Println(climbStairs(3))
    }
    ```