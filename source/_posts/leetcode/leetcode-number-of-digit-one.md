----
title: LeetCode：数字 1 的个数
date: 2021-03-08 22:50:05
categories: LeetCode
tags: 
- Go
toc: true
----

> 未完待续

## 题目

给定一个整数 `n`，计算所有小于等于 `n` 的非负整数中数字 1 出现的个数。

**示例 1：**

```
输入：n = 13
输出：6
```

**示例 2：**

```
输入：n = 0
输出：0
```

<!-- more -->

**提示：**

- `0 <= n <= 2 * 10^9`

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/number-of-digit-one

## 代码

### Go

- 代码一（执行超时，不可取）

    ```go
    package main

    import (
        "fmt"
        "math"
    )

    func countDigitOne(n int) int {
        var r int

        for i := 0; i <= n; i++ {
            // 尾数是1
            if t := i % 10; t == 1 {
                fmt.Println(i, "--", t)
                r += 1
            }

            // 十位、百位、千位等
            f := float64(i)
            for {
                if f < 10 {
                    break
                }

                if f = math.Floor(f / 10); int(f)%10 == 1 {
                    r += 1
                    fmt.Println(i, "::", f)
                }
            }
        }

        return r
    }

    func main() {
        // fmt.Println(countDigitOne(13))  // 6
        // fmt.Println(countDigitOne(20))  // 12
        // fmt.Println(countDigitOne(100)) // 21
        // fmt.Println(countDigitOne(110)) // 33
        fmt.Println(countDigitOne(824883294)) //
    }
    ```