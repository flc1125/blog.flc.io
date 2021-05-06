----
title: LeetCode：判断路径是否相交
date: 2021-05-06 23:10:44
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
toc: true
----

## 题目

给你一个字符串 `path`，其中 `path[i]` 的值可以是 `'N'`、`'S'`、`'E'` 或者 `'W'`，分别表示向北、向南、向东、向西移动一个单位。

机器人从二维平面上的原点 `(0, 0)` 处开始出发，按 `path` 所指示的路径行走。

如果路径在任何位置上出现相交的情况，也就是走到之前已经走过的位置，请返回 `True` ；否则，返回 `False` 。

<!-- more -->

**示例 1：**

![](https://s.flc.io/2021-05-06-23-12-38.png)

```
输入：path = "NES"
输出：false 
解释：该路径没有在任何位置相交。
```

**示例 2：**

![](https://s.flc.io/2021-05-06-23-12-53.png)

```
输入：path = "NESWW"
输出：true
解释：该路径经过原点两次。
```

**提示：**

- `1 <= path.length <= 10^4`
- `path` 仅由 `{'N', 'S', 'E', 'W}` 中的字符组成

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/path-crossing。

## 代码

### Go

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

type Point struct {
	x, y int
}

func isPathCrossing(path string) bool {
	var points map[string]Point

	points = make(map[string]Point)

	// 原点
	prev := Point{0, 0}
	points[pointToKey(prev)] = prev

	for _, p := range strings.Split(path, "") {
		next := nextPoint(prev, p)
		nextKey := pointToKey(next)

		if _, ok := points[nextKey]; ok {
			return true
		}

		points[nextKey] = next

		prev = next
	}

	// fmt.Println(points)

	return false
}

func pointToKey(point Point) string {
	return strconv.Itoa(point.x) + "_" + strconv.Itoa(point.y)
}

func nextPoint(point Point, p string) Point {
	switch p {
	case "N":
		return Point{x: point.x, y: point.y + 1}
	case "E":
		return Point{x: point.x + 1, y: point.y}
	case "S":
		return Point{x: point.x, y: point.y - 1}
	case "W":
		return Point{x: point.x - 1, y: point.y}
	}

	panic("Error")
}

func main() {
	fmt.Println(isPathCrossing("NES"))
	fmt.Println(isPathCrossing("NESWW"))
	fmt.Println(isPathCrossing("SS"))
}
```