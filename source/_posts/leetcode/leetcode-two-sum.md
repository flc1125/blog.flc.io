----
title: LeetCode：两数之和
date: 2021-3-3 21:28:20
categories: 编程
tags: 
- Go
- LeetCode
- PHP
toc: true
----

## 题目

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

你可以按任意顺序返回答案。

<!-- more -->

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例 2：**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```
 

提示：

- 2 <= nums.length <= 103
- -109 <= nums[i] <= 109
- -109 <= target <= 109
- **只会存在一个有效答案**

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/two-sum

## 代码

### Go

```go
package main

import "fmt"

func twoSum(nums []int, target int) []int {
	for i := 0; i < len(nums); i++ {
		for j := i; j < len(nums); j++ {
			if j > i && (nums[i]+nums[j] == target) {
				return []int{i, j}
			}
		}
	}

	return nil
}

func main() {
	fmt.Println(twoSum([]int{2, 7, 11, 15}, 9))
}

```

### PHP

```php
class Solution {

    /**
     * @param Integer[] $nums
     * @param Integer $target
     * @return Integer[]
     */
    function twoSum($nums, $target) {
        for ($i = 0; $i < count($nums); $i ++) {
            for ($j = $i; $j < count($nums); $j ++) {
                if ($j > $i && $nums[$i] + $nums[$j] == $target) {
                    return [$i, $j];
                }
            }
        }

        return false;
    }
}
```