----
title: LeetCode：变位词组
date: 2021-3-29 14:04:04
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

编写一种方法，对字符串数组进行排序，将所有变位词组合在一起。变位词是指字母相同，但排列不同的字符串。

**注意：本题相对原题稍作修改**

**示例:**

```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

<!-- more -->

**说明：**

- 所有输入均为小写字母。
- 不考虑答案输出的顺序。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/group-anagrams-lcci


## 代码

### Go

```go
package main

import (
	"fmt"
	"sort"
	"strings"
)

func groupAnagrams(strs []string) [][]string {
	var maps map[string][]string
	maps = make(map[string][]string)

	for _, v := range strs {
		key := stringToKey(v)
		maps[key] = append(maps[key], v)
	}

	var result [][]string
	result = make([][]string, 0, len(maps))

	for _, v := range maps {
		result = append(result, v)
	}

	return result
}

func stringToKey(str string) string {
	strs := strings.Split(str, "")

	sort.Strings(strs)

	return strings.Join(strs, "")
}

func main() {
	fmt.Println(groupAnagrams([]string{
		"eat", "tea", "tan", "ate", "nat", "bat",
	}))
}
```