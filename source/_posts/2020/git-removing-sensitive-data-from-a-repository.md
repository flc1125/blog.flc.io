----
title: GIT 从仓库中删除某文件及历史提交记录
date: 2020-09-28 00:12
categories: 编程
tags: 
- Git
- 工具
toc: true
----

## 命令

```bash
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch [文件路径]" \
  --prune-empty --tag-name-filter cat -- --all
```

<!-- more -->

## 参考文档

- https://docs.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository