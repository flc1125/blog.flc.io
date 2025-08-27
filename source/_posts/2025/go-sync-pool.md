----
title: "Go 中 sync.Pool 存储 *[]T vs []T 的性能对比研究"
cover: https://s.flc.io/202508271936971.png!blog
date: 2025-08-27 19:40:40
categories:
- 编程
toc: true
tags:
- Go
- Benchmark
----

## 背景

之前提交了一个功能，维护者后将一部分代码优化成[如此](https://github.com/open-telemetry/opentelemetry-go/blob/3342341f15081be03d23b3e36b9e2c07ffca858a/exporters/stdout/stdouttrace/trace.go#L102-L129)，印象中返回具体类型性能会更好，想验证为什么需要返回使用指针。

## 研究

直接上代码：

```go
package slices

import (
	"sync"
	"testing"
)

const appendN = 1000

var optPool = sync.Pool{
	New: func() any {
		ints := make([]int, 0, appendN)
		return &ints
	},
}

func BenchmarkAppendWithPool(b *testing.B) {
	b.ResetTimer()
	b.ReportAllocs()
	for i := 0; i < b.N; i++ {
		sl := optPool.Get().(*[]int)
		for j := 0; j < appendN; j++ {
			*sl = append(*sl, j)
		}
		*sl = (*sl)[:0]
		optPool.Put(sl)
	}
}

var optPool2 = sync.Pool{
	New: func() any {
		return make([]int, 0, appendN)
	},
}

func BenchmarkAppendWithPool2(b *testing.B) {
	b.ResetTimer()
	b.ReportAllocs()
	for i := 0; i < b.N; i++ {
		sl := optPool2.Get().([]int)
		for j := 0; j < appendN; j++ {
			sl = append(sl, j)
		}
		sl = sl[:0]
		optPool2.Put(sl)
	}
}
```

基准测试结果：

<!-- more -->

```
goos: darwin
goarch: arm64
pkg: golangdemo/slices
cpu: Apple M4
BenchmarkAppendWithPool
BenchmarkAppendWithPool-10     	  660138	      1794 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithPool2
BenchmarkAppendWithPool2-10    	 3828464	       309.8 ns/op	      24 B/op	       1 allocs/op
```

从结果看：Pool2 会明显比 Pool 更快。但从理论上不符合预期，`*sl = append(*sl, j)` 的执行，因为指针解引用，按道理会有 CPU 的性能损耗。

---

继续研究：将 `appendN` 改为 3，结局令人意外，性能反转：

```
goos: darwin
goarch: arm64
pkg: golangdemo/slices
cpu: Apple M4
BenchmarkAppendWithPool
BenchmarkAppendWithPool-10     	161565705	         7.389 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithPool2
BenchmarkAppendWithPool2-10    	67736814	        23.26 ns/op	      24 B/op	       1 allocs/op
PASS
```

调整代码，进行多维度的验证，代码部分：

```go
func BenchmarkAppendWithTests(b *testing.B) {
	for _, n := range []int{1, 5, 10, 50, 100, 500, 1000, 5000} {
		p1 := sync.Pool{
			New: func() any {
				ints := make([]int, 0, n)
				return &ints
			},
		}

		p2 := sync.Pool{
			New: func() any {
				return make([]int, 0, n)
			},
		}

		b.Run(fmt.Sprintf("P1-%d", n), func(b *testing.B) {
			b.ResetTimer()
			b.ReportAllocs()
			for i := 0; i < b.N; i++ {
				sl := p1.Get().(*[]int)
				for j := 0; j < n; j++ {
					*sl = append(*sl, j)
				}
				*sl = (*sl)[:0]
				p1.Put(sl)
			}
		})

		b.Run(fmt.Sprintf("P2-%d", n), func(b *testing.B) {
			b.ResetTimer()
			b.ReportAllocs()
			for i := 0; i < b.N; i++ {
				sl := p2.Get().([]int)
				for j := 0; j < n; j++ {
					sl = append(sl, j)
				}
				sl = sl[:0]
				p2.Put(sl)
			}
		})
	}
}
```

基准结果：

```
goos: darwin
goarch: arm64
pkg: golangdemo/slices
cpu: Apple M4
BenchmarkAppendWithTests
BenchmarkAppendWithTests/P1-1
BenchmarkAppendWithTests/P1-1-10         	167235411	         6.690 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithTests/P2-1
BenchmarkAppendWithTests/P2-1-10         	69313578	        18.26 ns/op	      24 B/op	       1 allocs/op
BenchmarkAppendWithTests/P1-5
BenchmarkAppendWithTests/P1-5-10         	100000000	        12.03 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithTests/P2-5
BenchmarkAppendWithTests/P2-5-10         	65082980	        19.02 ns/op	      24 B/op	       1 allocs/op
BenchmarkAppendWithTests/P1-10
BenchmarkAppendWithTests/P1-10-10        	49169329	        23.37 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithTests/P2-10
BenchmarkAppendWithTests/P2-10-10        	58869218	        20.01 ns/op	      24 B/op	       1 allocs/op
BenchmarkAppendWithTests/P1-50
BenchmarkAppendWithTests/P1-50-10        	13451947	        88.70 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithTests/P2-50
BenchmarkAppendWithTests/P2-50-10        	37012665	        30.89 ns/op	      24 B/op	       1 allocs/op
BenchmarkAppendWithTests/P1-100
BenchmarkAppendWithTests/P1-100-10       	 6741718	       178.8 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithTests/P2-100
BenchmarkAppendWithTests/P2-100-10       	25654273	        48.04 ns/op	      24 B/op	       1 allocs/op
BenchmarkAppendWithTests/P1-500
BenchmarkAppendWithTests/P1-500-10       	 1351078	       915.9 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithTests/P2-500
BenchmarkAppendWithTests/P2-500-10       	 6742482	       180.2 ns/op	      24 B/op	       1 allocs/op
BenchmarkAppendWithTests/P1-1000
BenchmarkAppendWithTests/P1-1000-10      	  664776	      1788 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithTests/P2-1000
BenchmarkAppendWithTests/P2-1000-10      	 3937927	       307.3 ns/op	      24 B/op	       1 allocs/op
BenchmarkAppendWithTests/P1-5000
BenchmarkAppendWithTests/P1-5000-10      	  134226	      8871 ns/op	       0 B/op	       0 allocs/op
BenchmarkAppendWithTests/P2-5000
BenchmarkAppendWithTests/P2-5000-10      	  881137	      1374 ns/op	      24 B/op	       1 allocs/op
PASS
```

附录 ChatGPT 生成的性能走势图：

![](https://s.flc.io/202508271936971.png)

## 结论

- 指针的的操作因为无分配，所以无额外的内存分配，但会存在 CPU 运算。
- 但指针的操作在小切片中表现不错；在大切片中 CPU 性能损耗超越内存分配带来的性能损耗，大切片下适合P2方法。
- 分水岭在 10，预估与 CPU 核心数有关。Apple M4 刚好就是 10 核心。