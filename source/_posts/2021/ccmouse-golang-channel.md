----
title: 笔记：搭建并行处理管道，感受GO语言魅力（未完结）
date: 2021-05-17 21:27:05
updated: 2021-05-17 22:49:03
categories: [编程,后端]
tags: 
- Go
- 笔记
toc: true
----

## 前言

团队内组织 Golang 的学习，这是第三周学习内容：完成课程的学习、代码编写与 Channel 心得总结。

**相关课程**

https://www.imooc.com/learn/927

## 内容

### Hello World 网络版

**代码：**

```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "sdfasdfas %s", r.FormValue("tests"))
	})

	http.ListenAndServe(":1000", nil)
}
```

<!-- more -->

访问：http://localhost:1000/?tests=123123

![](https://s.flc.io/2021-05-17-21-29-13.png)

**压测数据：**

|压测次数|第一次|第二次|第三次|
|----|----|----|----|
| Requests/sec | 98652.72 | 104031.37 | 107409.94|

```bash
# 第一次
➜  ~ wrk -t 10 -c 100 -d 30s http://localhost:1000/\?tests\=123123
Running 30s test @ http://localhost:1000/?tests=123123
  10 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.57ms    3.78ms 128.84ms   95.94%
    Req/Sec     9.93k     2.11k   17.24k    66.03%
  2968564 requests in 30.09s, 376.53MB read
Requests/sec:  98652.72
Transfer/sec:     12.51MB

# 第二次
➜  ~ wrk -t 10 -c 100 -d 30s http://localhost:1000/\?tests\=123123
Running 30s test @ http://localhost:1000/?tests=123123
  10 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.22ms    1.91ms  73.69ms   94.46%
    Req/Sec    10.47k     1.42k   19.09k    79.43%
  3131585 requests in 30.10s, 397.21MB read
Requests/sec: 104031.37
Transfer/sec:     13.20MB

# 第三次
➜  ~ wrk -t 10 -c 100 -d 30s http://localhost:1000/\?tests\=123123
Running 30s test @ http://localhost:1000/?tests=123123
  10 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.03ms    0.97ms  34.92ms   91.61%
    Req/Sec    10.80k   788.52    15.52k    78.32%
  3233409 requests in 30.10s, 410.12MB read
Requests/sec: 107409.94
Transfer/sec:     13.62MB
```

### Hello World 并发版

**普通版**

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	normal()
}

func nogo() {
	for i := 0; i < 5000; i++ {
		go func(ii int) {
			fmt.Println("Hello world", ii)
		}(i)
	}

	time.Sleep(time.Second * 2)
}
```

**协程版**

```go
package main

import (
	"fmt"
	"strconv"
	"time"
)

func main() {
	channel()
}

func channel() {
	ch := make(chan string)

	for i := 0; i < 5000; i++ {
		go func(ii int, ch chan string) {
			for {
				ch <- "Hello world" + strconv.Itoa(ii)
			}
		}(i, ch)
	}

	for {
		fmt.Println(<-ch)
	}
}
```

## Channel 总结

**基本用法**

```go
ch := make(chan string) // 创建一个字符串的通道

msg := <-ch  // 通道出队

ch <- "string" // 通道入队
```

……

## 参考文档

- Go Channel 详解：https://www.runoob.com/w3cnote/go-channel-intro.html