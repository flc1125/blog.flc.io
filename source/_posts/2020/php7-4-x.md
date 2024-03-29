----
title: PHP 各版本特性：PHP 7.4.x
date: 2020-06-02 00:34:00
categories: 编程
tags:
- PHP
toc: true
----

> 本文部分内容根据官网进行删减，仅整理常用特性。

## 新特性

### 类属性

类属性现在支持类型声明。

```php
<?php

class User
{
    public int $id;

    public string $name;
}
```

<!-- more -->

上面的示例将强制 `$user->id` 只能分配 `int` 值，而 `$user->name` 只能分配 `string` 值。

### 箭头函数

[箭头函数](https://www.php.net/manual/zh/functions.arrow.php) 提供了用于定义具有隐式按值作用域绑定的函数的简写语法。

```php
<?php

$factor = 10;
$nums = array_map(fn($n) => $n * $factor, [1, 2, 3, 4]);
// $nums = array(10, 20, 30, 40);
```

### 协变返回和逆变参数

```php
<?php

class A {}
class B extends A {}

class Producer
{
    public function method(): A {}
}

class ChildProducer extends Producer
{
    public function method(): B {}
}
```

### `null` 赋值运算符

```php
<?php

$array['key'] ??= computeDefault();
// 等同于
if (!isset($array['key'])) {
    $array['key'] = computeDefault();
}
```

### 打包内部数组

```php
<?php

$parts = ['apple', 'pear'];
$fruits = ['banana', 'orange', ...$parts, 'watermelon'];
// ['banana', 'orange', 'apple', 'pear', 'watermelon'];
```

### OPcache

添加了对预加载代码的支持。

### 自定义对象序列化

添加了用于自定义对象序列化的新机制，该机制使用了两个新的魔术方法：`__serialize` 和 `__unserialize`。

```php
<?php
// Returns array containing all the necessary state of the object.
public function __serialize(): array;

// Restores the object state from the given data array.
public function __unserialize(array $data): void;
```

### 无参数的合并数组函数

现在可以不带任何参数地调用 `array_merge()` 和 `array_merge_recursive()`，在这种情况下，它们将返回一个空数组。与传播运算符（例如 `array_merge(...$arrays)`) 结合使用时很有用。

### [proc_open()](https://www.php.net/manual/zh/function.proc-open.php) 函数

[proc_open()](https://www.php.net/manual/zh/function.proc-open.php) 现在接受命令数组而不是字符串。在这种情况下，该过程将直接打开（无需通过shell），PHP将处理任何必要的参数转义。

```php
<?php

proc_open(['php', '-r', 'echo "Hello World\n";'], $descriptors, $pipes);
```

现在支持 重定向 和 空描述符。

```php
<?php
// Like 2>&1 on the shell
proc_open($cmd, [1 => ['pipe', 'w'], 2 => ['redirect', 1]], $pipes);
// Like 2>/dev/null or 2>nul on the shell
proc_open($cmd, [1 => ['pipe', 'w'], 2 => ['null']], $pipes);
```

----

**原文**

- 新特性：https://www.php.net/manual/zh/migration74.new-features.php