----
title: LeetCode：删除字符串中的所有相邻重复项
date: 2021-3-9 13:00:08
categories: [编程,LeetCode]
tags: 
- Go
- LeetCode
- PHP
toc: true
----

## 题目

给出由小写字母组成的字符串 S， **重复项删除操作** 会选择两个相邻且相同的字母，并删除它们。

在 S 上反复执行重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

<!-- more -->

**示例：**

```
输入："abbaca"
输出："ca"
解释：例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。
之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。
```
 

提示：

- `1 <= S.length <= 20000`
- `S` 仅由小写英文字母组成。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string

## 思路

注意审题： **两个相邻且相同的字母**；使用正则引用。

## 代码

### Go

```go
package main

import (
	"fmt"
)

func removeDuplicates(S string) string {
	for i := 0; i < len(S)-1; i++ {
		if S[i] == S[i+1] {
			return removeDuplicates((S[:i]) + (S[(i + 2):]))
		}
	}

	return S
}

func main() {
	fmt.Println(removeDuplicates("abbaca"))
	fmt.Println(removeDuplicates("aaaaaaaaa"))
}
```

Go 不支持正则引用

### PHP

```php
<?php

class Solution {

    /**
     * @param String $S
     * @return String
     */
    function removeDuplicates($S) {
        $r = preg_replace('/([a-z])\1{1}/u', '', $S);

        if ($r === $S)  {
            return $r;
        }

        return $this->removeDuplicates($r);
    }
}

$s = new Solution();
print_r($s->removeDuplicates('abbaca').PHP_EOL);
print_r($s->removeDuplicates("aaaaaaaaa").PHP_EOL);
```