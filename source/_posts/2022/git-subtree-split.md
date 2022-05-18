----
title: 通过 git subtree split 进行仓库拆分
date: 2022-05-18 09:36:42
categories: [编程,工具]
tags:
- Git
toc: true
----

## 先看效果

- 拆仓库前：https://github.com/laravel/framework/tree/9.x/src/Illuminate

    ![](https://s.flc.io/2022-05-18-10-20-04.png)

- 拆仓库后：https://github.com/illuminate

    ![](https://s.flc.io/2022-05-18-10-21-09.png)
    
## `split.sh` 拆包脚本

> 放置主仓库的 `/bin` 目录下

```bash
#!/usr/bin/env bash

# Usage:
# ./bin/split.sh [name]
#
# Example:
# ./bin/split.sh support

set -e
set -x

CURRENT_BRANCH=$(git rev-parse --abbrev-ref HEAD)
BASEPATH=$(cd `dirname $0`; cd ../src/; pwd)
REPOS=$@

function split()
{
    SHA1=`git subtree split --prefix=$1`
    git push $2 "$SHA1:refs/heads/$CURRENT_BRANCH" -f
}

function remote()
{
    git remote add $1 $2 || true
}

git pull origin $CURRENT_BRANCH

if [[ $# -eq 0 ]]; then
    REPOS=$(ls $BASEPATH)
fi

for REPO in $REPOS ; do
    remote $REPO git@github.com:org/$REPO.git # org 改为你自己的组织名或用户名

    split "src/$REPO" $REPO
done
```

<!-- more -->

## 使用

> 以下以：`github.com/org/framework` 作为主仓库演示；同时，你已经创建了 `github.com/org/a`

- 主仓库目录结构

    ```bash
    .
    ├── bin
    │   └── split.sh
    └── src
        ├── a
        │   ├── aa.txt
        │   ├── aaa.txt
        ├── b
        │   ├── b.txt
        │   ├── bb.txt
    ```

- 执行拆分

    ```bash
    ./bin/split.sh a
    ```

- 结果输出

    > 示例数据
    
    ```bash
    ++ git rev-parse --abbrev-ref HEAD
    + CURRENT_BRANCH=master
    +++ dirname ./bin/split.sh
    ++ cd ./bin
    ++ cd ../src/
    ++ pwd
    + BASEPATH=/path/org/framework/src
    + REPOS=a
    + git pull origin master
    来自 github.com:org/framework
     * branch            master     -> FETCH_HEAD
    已经是最新的。
    + [[ 1 -eq 0 ]]
    + for REPO in '$REPOS'
    + remote container git@github.com:org/container.git
    + git remote add container git@github.com:org/container.git
    error: 远程 container 已经存在。
    + true
    + split src/container a
    ++ git subtree split --prefix=src/a
    + SHA1=7fa9040b2282d……912bd1df4925d
    + git push a 7fa9040b2282d……912bd1df4925d:refs/heads/master -f
    枚举对象中: 5, 完成.
    对象计数中: 100% (5/5), 完成.
    使用 8 个线程进行压缩
    压缩对象中: 100% (3/3), 完成.
    写入对象中: 100% (3/3), 342 字节 | 342.00 KiB/s, 完成.
    总共 3（差异 2），复用 0（差异 0），包复用 0
    remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
    To github.com:org/a.git
       5326f10..7fa9040  7fa9040b2282d……912bd1df4925d -> master
    (base) ➜  framework git:(master) git push origin master
    枚举对象中: 11, 完成.
    对象计数中: 100% (11/11), 完成.
    使用 8 个线程进行压缩
    压缩对象中: 100% (6/6), 完成.
    写入对象中: 100% (6/6), 625 字节 | 625.00 KiB/s, 完成.
    总共 6（差异 3），复用 0（差异 0），包复用 0
    remote: Resolving deltas: 100% (3/3), completed with 3 local objects.
    To github.com:org/framework.git
       6672966..070004f  master -> master
    ```

## 原理

- 添加子包的远程仓库地址

    ```bash
    git remote add a github.com/org/a
  
    # 查看包方式：git remote -v
    ```

- 通过 `git subtree split --prefix=src/a` 方式获取子仓库的 `commit id` (即下文的 $SHA1)
- 通过 `git push $2 "$SHA1:refs/heads/master" -f` 强推到远程子仓库

## 其他

- 网络看到的资料说 `git subtree split` 在早期版本有些问题，会存在历史记录丢失的情况；
- 所以早期的方案：https://github.com/splitsh/lite，具体还未研究。

## 参考资料

- splitsh-lite:https://github.com/splitsh/lite
- Laravel:https://github.com/laravel/framework/blob/9.x/bin/split.sh
- Hyperf:https://github.com/hyperf/hyperf/blob/master/bin/split.sh