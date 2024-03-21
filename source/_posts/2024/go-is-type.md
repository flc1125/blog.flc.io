title: Go：一种很“新”的类型断言
cover: https://s.flc.io/202403210858075.png
date: 2024-03-21 08:31:33
categories:
    - 编程
tags:
- Go
- type
----

> 分享组件：[go-kratos-ecosystem/components](https://github.com/go-kratos-ecosystem/components)
> 
> 注：该功能计划在 v2.11.0 版本中发布。

## 引言

我们在早期使用 Go 做类型断言的时候，大多是这么用：

```go
var v interface{}
v = 10

if _, ok := v.(int); ok {
    fmt.Println("v is int")
}
```

那现在，我们只需要这么用：

```go
if IsType[int](v) {
    fmt.Println("v is int")
}
```

<!--more-->

## 主角

这就是今天要给大家分享的一个新方法：`IsType`。这个想法来自于 [关于增加 `is[T any](any) bool` 的提案](https://github.com/golang/go/issues/65846) 。

我们先看看实际的代码和单测效果。

- 代码：

    ```go
    func IsType[T any](value any) bool {
        _, ok := value.(T)
        return ok
    }
    ```

- 单测：

    ```go
    func TestIsType(t *testing.T) {
        assert.True(t, IsType[int](10))
        assert.False(t, IsType[int](int8(10)))
    
        assert.True(t, IsType[string]("foo"))
        assert.False(t, IsType[string](10))
    
        assert.True(t, IsType[time.Time](time.Now()))
        assert.False(t, IsType[time.Time](10))
    
        assert.True(t, IsType[foo](foo{}))
        assert.False(t, IsType[foo](10))
    
        assert.True(t, IsType[*foo](&foo{}))
        assert.False(t, IsType[foo](&foo{}))
        assert.False(t, IsType[*foo](nil))
    
        assert.True(t, IsType[testInterface](testStruct{}))
        assert.True(t, IsType[interface{}](testStruct{}))
        assert.True(t, IsType[any](testStruct{}))
    
        assert.True(t, IsType[error](errors.New("foo")))
        assert.False(t, IsType[error](nil))
    }
    ```

- 单测结果：
    
    ```bash
    === RUN   TestIsType
    --- PASS: TestIsType (0.00s)
    PASS
    ```

底层其实就是应用了 Go 1.18+ 中的泛型特性，通过这种方式，我们可以很方便地判断一个值的类型。原来三行代码，现在只需要一行代码就可以搞定。

```go
// 之前
var isType bool
if _, ok := v.(int); ok {
    isType = true
}

// 但现在
isType = IsType[int](v)
```

## 性能

考虑到使用了泛型，我们来比较下使用原生和使用泛型的性能损耗。

> 相关脚本：https://github.com/go-kratos-ecosystem/components/pull/185/files

- 原生：

    单测脚本：

    ```go
    func testIsType(value any) bool {
        _, ok := value.(int)
        return ok
    }
    
    func BenchmarkIsType_Native(b *testing.B) {
        b.RunParallel(func(pb *testing.PB) {
            for pb.Next() {
                testIsType(10)
            }
        })
    }
    ```
    
    单测结果：

    ```bash
    BenchmarkIsType_Native
    BenchmarkIsType_Native-8   	1000000000	         0.2330 ns/op
    ```

- 泛型：

    单测脚本：

    ```go
    func BenchmarkIsType_Generics(b *testing.B) {
        b.RunParallel(func(pb *testing.PB) {
            for pb.Next() {
                IsType[int](10)
            }
        })
    }
    ```

    单测结果：

    ```bash
    BenchmarkIsType_Generics
    BenchmarkIsType_Generics-8   	1000000000	         0.2403 ns/op
    ```
  
可以看到，性能损耗几乎可以忽略不计。

## 应用

实际应用中，类似很多语言的 `instanceof` 操作，我们可以使用这种方式来判断类型。

```go
type Named interface {
    Name() string
}

type Person struct {
    Name string
}

func (p Person) Name() string {
    return p.Name
}

func main() {
    var n Named = Person{"foo"}
    if IsType[Person](n) {
        fmt.Println(n.Name())
    }
}
```

## 参考资料

- [关于增加 `is[T any](any) bool` 的提案](https://github.com/golang/go/issues/65846)
- [Go 1.18 泛型](https://golang.org/doc/go1.18#generics)
- [Go Kratos Components](https://github.com/go-kratos-ecosystem/components)
- [`values.IsType`](https://github.com/go-kratos-ecosystem/components/blob/fdbd551b1a4c2da976618421d8ff953dd35e602f/values/values.go#L140-L147)
- [性能测试脚本](https://github.com/go-kratos-ecosystem/components/pull/185/files)
- [PR: Add IsType](https://github.com/go-kratos-ecosystem/components/pull/184)