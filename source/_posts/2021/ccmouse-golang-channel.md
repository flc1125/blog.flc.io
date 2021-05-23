----
title: 笔记：搭建并行处理管道，感受GO语言魅力
date: 2021-05-17 21:27:05
updated: 2021-05-23 23:11:51
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

## 基础编程

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

### 简单排序

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	var ints []int = []int{1, 8, 2, 5, 3, 10, 3, 4}

	sort.Ints(ints)

	fmt.Println(ints)
}
```

## 归并排序算法

![](https://s.flc.io/2021-05-19-21-38-37.png)

```go
package main

import (
	"fmt"
	"sort"
)

func arraySource(in ...int) <-chan int {
	out := make(chan int)

	go func() {
		for _, i := range in {
			out <- i
		}
		close(out)
	}()

	return out
}

func arraySort(in <-chan int) <-chan int {
	out := make(chan int)

	go func() {
		temp := []int{}

		// 此处注意，无下标
		for v := range in {
			temp = append(temp, v)
		}

		sort.Ints(temp)

		// 此处注意下标
		for _, v := range temp {
			out <- v
		}

		close(out)
	}()

	return out
}

func arrayMerge(in1, in2 <-chan int) <-chan int {
	out := make(chan int)

	go func() {
		for {
			v1, ok1 := <-in1
			v2, ok2 := <-in2

			if !ok1 && !ok2 {
				break
			}

			if ok1 && ok2 {
				if v1 <= v2 {
					out <- v1
				} else {
					out <- v2
				}
				continue
			}

			if ok1 && !ok2 {
				out <- v1
			} else {
				out <- v2
			}

		}

		close(out)
	}()

	return out
}

func main() {
	// 方法1
	p := arraySource(1, 2, 3, 4, 5)

	for {
		if v, ok := <-p; ok {
			fmt.Println(v)
		} else {
			break
		}
	}
	fmt.Println("--------------------------------")

	// 方法二：直接循环通道
	p2 := arraySource(1, 2, 3, 4, 5)

	for v := range p2 {
		fmt.Println(v)
	}
	fmt.Println("--------------------------------")

	// 协程排序
	p3 := arraySort(arraySource(5, 6, 21, 1, 7, 10))

	for v := range p3 {
		fmt.Println(v)
	}
	fmt.Println("--------------------------------")

	// 归并节点
	p4 := arrayMerge(
		arraySort(arraySource(5, 6, 21, 1, 7, 10)),
		arraySort(arraySource(15, 6, 21, 4, 3, 100)),
	)

	for v := range p4 {
		fmt.Println(v)
	}
}
```

输出

```
1
2
3
4
5
--------------------------------
1
2
3
4
5
--------------------------------
1
5
6
7
10
21
--------------------------------
1
4
6
7
10
21
```

## 文件读写

```go
package main

import (
	"bufio"
	"encoding/binary"
	"fmt"
	"io"
	"math/rand"
	"os"
)

func readSource(r io.Reader) <-chan int {
	out := make(chan int)

	go func() {
		buffer := make([]byte, 8)

		for {
			n, err := r.Read(buffer)

			if n > 0 {
				out <- int(binary.BigEndian.Uint64(buffer))
			}

			if err != nil {
				break
			}
		}

		close(out)
	}()

	return out
}

func writeSource(w io.Writer, in <-chan int) {
	for v := range in {
		buffer := make([]byte, 8)

		binary.BigEndian.PutUint64(buffer, uint64(v))

		w.Write(buffer)
	}
}

func randomSource(count int) <-chan int {
	out := make(chan int)

	go func() {
		for count > 0 {
			out <- rand.Intn(10)
			count--
		}

		close(out)
	}()

	return out
}

func main() {
	// 生成随机数
	p := randomSource(50)

	for v := range p {
		fmt.Println(v)
	}

	// 文件读写
	// 写入文件
	file, err := os.Create("tests.log")
	if err != nil {
		panic("Error")
	}
	defer file.Close()

	randints := randomSource(100)
	writer := bufio.NewWriter(file)
	writeSource(writer, randints)
	writer.Flush()

	// 文件读取
	file2, err2 := os.Open("tests.log")
	if err2 != nil {
		panic("Error")
	}
	defer file2.Close()
	p3 := readSource(bufio.NewReader(file2))
	for v := range p3 {
		fmt.Println(v)
	}
}
```

> 一堆新方法和函数……

## 网络版外部排序

> 大意：将数据通过网络请求到服务端；在另外通过客户端请求服务端，将数据读取

## 总结

**channel 相关**

```go
ch := make(chan string) // 创建一个字符串的通道

msg := <-ch  // 通道出队

ch <- "string" // 通道入队

// [通过缓存的使用，可以尽量避免阻塞，提供应用的性能]
// 设置 channel 最多容纳元素的数量（该示例为100）；
// 如果没有设置容量，或者容量设置为0, 说明Channel没有缓存；
ch := make(chan string, 100)

close(ch) // close方法可以关闭Channel。

v, ok := <-ch  // ok 可判定 channel 是否被关闭

// 直接读取 channel 内容，直到 channel 被关闭
for v := range ch {
	fmt.Println(i)
}
```

- 其他 `defer` 先进后出
- `go tool tour` 可进入交互式调试界面，若无法使用，请安装 `go get golang.org/x/tour/gotour`
- 中文版（非官方）文档：http://docscn.studygolang.com/doc/
  - https://github.com/golang/tour/

## 参考文档

- Go Channel 详解：https://www.runoob.com/w3cnote/go-channel-intro.html