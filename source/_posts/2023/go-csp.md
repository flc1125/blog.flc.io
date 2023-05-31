----
title: 分享：CSP 模型
cover: https://s.flc.io/202305310837273.png!blog
date: 2023-05-31 08:30:29
categories: 
    - 分享
----

> CSP 是 Communicating Sequential Process 的简称，中文直译为通信顺序进程，或者叫做交换信息的循序进程，是用来描述并发系统中进行交互的一种模式。
> 
> CSP 最早出现于计算机科学家 Tony Hoare 在 1978 年发表的论文中（你可能不熟悉 Tony Hoare 这个名字，但是你一定很熟悉排序算法中的 Quicksort 算法，他就是 Quicksort 算法的作者，图灵奖的获得者）。最初，论文中提出的 CSP 版本在本质上不是一种进程演算，而是一种并发编程语言，但之后又经过了一系列的改进，最终发展并精炼出 CSP 的理论。**CSP 允许使用进程组件来描述系统，它们独立运行，并且只通过消息传递的方式通信。**
> 
> 就像 Go 的创始人之一 Rob Pike 所说的：“每一个计算机程序员都应该读一读 Tony Hoare 1978 年的关于 CSP 的论文。”他和 Ken Thompson 在设计 Go 语言的时候也深受此论文的影响，并将 CSP 理论真正应用于语言本身（Russ Cox 专门写了一篇文章记录这个历史），通过引入 Channel 这个新的类型，来实现 CSP 的思想。
>
> —— [Go 并发编程实战课-Channel](https://time.geekbang.org/column/article/304188)

<!--more-->

**参考资料：**

- Go 并发编程实战课-Channel：https://time.geekbang.org/column/article/304188
- CSP 论文：https://www.cs.cmu.edu/~crary/819-f09/Hoare78.pdf
  - 为避免链接失效，副本：https://s.flc.io/202305310832107.pdf
- 贝尔实验室和 CSP 线程：https://swtch.com/~rsc/thread/