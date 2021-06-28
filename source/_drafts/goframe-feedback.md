----
title: 基于 goFrame 框架实现的留言板 Demo
date: 2021-05-24 21:45:05
updated: 2021-05-28 23:25:39
categories: [编程,后端]
tags: 
- Go
toc: true
----



## 压测

```bash
➜  ~ wrk -c 100 -t 10 -d 30s http://localhost:8199/hello/index
Running 30s test @ http://localhost:8199/hello/index
  10 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.29ms    4.45ms 166.88ms   97.65%
    Req/Sec     5.48k     1.17k    7.55k    72.50%
  1636024 requests in 30.01s, 227.79MB read
Requests/sec:  54511.01
Transfer/sec:      7.59MB
```