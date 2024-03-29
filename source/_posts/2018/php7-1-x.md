----
title: PHP 各版本特性：PHP 7.1.x
date: 2018-08-13 22:08
categories: 编程
tags:
- PHP
toc: true
----

> 本文部分内容根据官网进行删减，仅整理常用特性。

## 新特性

### 可为空（Nullable）类型

参数以及返回值的类型现在可以通过在类型前加上一个问号使之允许为空。 当启用这个特性时，传入的参数或者函数返回的结果要么是给定的类型，要么是 [null](http://php.net/manual/zh/language.types.null.php) 。

<!-- more -->

```php
<?php

function testReturn(): ?string
{
    return 'elePHPant';
}

var_dump(testReturn());

function testReturn(): ?string
{
    return null;
}

var_dump(testReturn());

function test(?string $name)
{
    var_dump($name);
}

test('elePHPant');
test(null);
test();
```

以上例程会输出：

```
string(10) "elePHPant"
NULL
string(10) "elePHPant"
NULL
Uncaught Error: Too few arguments to function test(), 0 passed in...
```

### Void 函数

一个新的返回值类型 `void` 被引入。 返回值声明为 `void` 类型的方法要么干脆省去 `return` 语句，要么使用一个空的 return 语句。 对于 `void` 函数来说，`NULL` 不是一个合法的返回值。

```php
<?php
function swap(&$left, &$right) : void
{
    if ($left === $right) {
        return;
    }

    $tmp = $left;
    $left = $right;
    $right = $tmp;
}

$a = 1;
$b = 2;
var_dump(swap($a, $b), $a, $b);
```

以上例程会输出：

```
null
int(2)
int(1)
```

试图去获取一个 `void` 方法的返回值会得到 `NULL` ，并且不会产生任何警告。这么做的原因是不想影响更高层次的方法。

### Symmetric array destructuring

短数组语法（`[]`）现在作为 `list()` 语法的一个备选项，可以用于将数组的值赋给一些变量（包括在 `foreach` 中）。

```php
<?php
$data = [
    [1, 'Tom'],
    [2, 'Fred'],
];

// list() style
list($id1, $name1) = $data[0];

// [] style
[$id1, $name1] = $data[0];

// list() style
foreach ($data as list($id, $name)) {
    // logic here with $id and $name
}

// [] style
foreach ($data as [$id, $name]) {
    // logic here with $id and $name
}
```

### 类常量可见性

现在起支持设置类常量的可见性。

```php
<?php
class ConstDemo
{
    const PUBLIC_CONST_A = 1;
    public const PUBLIC_CONST_B = 2;
    protected const PROTECTED_CONST = 3;
    private const PRIVATE_CONST = 4;
}
```

### iterable 伪类

现在引入了一个新的被称为 iterable 的伪类 (与 [callable](http://php.net/manual/zh/language.types.callable.php) 类似)。 这可以被用在参数或者返回值类型中，它代表接受数组或者实现了 Traversable 接口的对象。 至于子类，当用作参数时，子类可以收紧父类的 iterable 类型到 [array](http://php.net/manual/zh/language.types.array.php) 或一个实现了 Traversable 的对象。对于返回值，子类可以拓宽父类的 [array](http://php.net/manual/zh/language.types.array.php) 或对象返回值类型到 iterable。

```php
<?php
function iterator(iterable $iter)
{
    foreach ($iter as $val) {
        //
    }
}
```

### 多异常捕获处理

一个 `catch` 语句块现在可以通过管道字符(`|`)来实现多个异常的捕获。 这对于需要同时处理来自不同类的不同异常时很有用。

```php
<?php
try {
    // some code
} catch (FirstException | SecondException $e) {
    // handle first and second exceptions
}
```

### [list()](http://php.net/manual/zh/function.list.php) 现在支持键名

现在 [list()](http://php.net/manual/zh/function.list.php) 和它的新的 `[]` 语法支持在它内部去指定键名。这意味着它可以将任意类型的数组 都赋值给一些变量（与短数组语法类似）

```php
<?php
$data = [
    ["id" => 1, "name" => 'Tom'],
    ["id" => 2, "name" => 'Fred'],
];

// list() style
list("id" => $id1, "name" => $name1) = $data[0];

// [] style
["id" => $id1, "name" => $name1] = $data[0];

// list() style
foreach ($data as list("id" => $id, "name" => $name)) {
    // logic here with $id and $name
}

// [] style
foreach ($data as ["id" => $id, "name" => $name]) {
    // logic here with $id and $name
}
```

### 支持为负的字符串偏移量

现在所有支持偏移量的字符串操作函数 都支持接受负数作为偏移量，包括通过 `[]`或 `{}` 操作字符串下标。在这种情况下，一个负数的偏移量会被理解为一个从字符串结尾开始的偏移量。

```php
<?php
var_dump("abcdef"[-2]);
var_dump(strpos("aabbcc", "b", -3));
```

以上例程会输出：

```
string (1) "e"
int(3)
```

Negative string and array offsets are now also supported in the simple variable parsing syntax inside of strings.

```php
<?php
$string = 'bar';
echo "The last character of '$string' is '$string[-1]'.\n";
?>
```

以上例程会输出：

```
The last character of 'bar' is 'r'.
```


----

**原文**

- 新特性：http://php.net/manual/zh/migration71.new-features.php