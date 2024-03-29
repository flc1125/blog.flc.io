----
title: PHP 各版本特性：PHP 7.2.x
date: 2018-08-21 08:44:22
categories: 编程
tags:
- PHP
toc: true
----

> 本文部分内容根据官网进行删减，仅整理常用特性。

##  新特性

### 新的对象类型

这种新的对象类型, [object](http://php.net/manual/zh/language.types.object.php), 引进了可用于逆变（contravariant）参数输入和协变（covariant）返回任何对象类型。

```php
<?php

function test(object $obj) : object
{
    return new SplQueue();
}

test(new StdClass());
```

<!-- more -->

### 允许重写抽象方法(Abstract method)

当一个抽象类继承于另外一个抽象类的时候，继承后的抽象类可以重写被继承的抽象类的抽象方法。

```php
<?php

abstract class A
{
    abstract function test(string $s);
}
abstract class B extends A
{
    // overridden - still maintaining contravariance for parameters and covariance for return
    abstract function test($s) : int;
}
```

### 扩展了参数类型

重写方法和接口实现的参数类型现在可以省略了。不过这仍然是符合LSP，因为现在这种参数类型是逆变的。

```php
<?php

interface A
{
    public function Test(array $input);
}

class B implements A
{
    public function Test($input){} // type omitted for $input
}
```

### 允许分组命名空间的尾部逗号

命名空间可以在PHP 7中使用尾随逗号进行分组引入。

```php
<?php

use Foo\Bar\{
    Foo,
    Bar,
    Baz,
};
```

## 废弃的功能

### 不带引号的字符串

不带引号的字符串是不存在的全局常量，转化成他们自身的字符串。 在以前，该行为会产生 `E_NOTICE`，但现在会产生 `E_WARNING`。在下一个 PHP 主版本中，将抛出 `Error` 异常。

```php
<?php

var_dump(NONEXISTENT);

/* Output:
Warning: Use of undefined constant NONEXISTENT - assumed 'NONEXISTENT' (this will throw an Error in a future version of PHP) in %s on line %d
string(11) "NONEXISTENT"
*/
```

### [__autoload()](http://php.net/manual/zh/function.autoload.php) 方法

[__autoload()](http://php.net/manual/zh/function.autoload.php) 方法已被废弃， 因为和 [spl\_autoload\_register()](http://php.net/manual/zh/function.spl-autoload-register.php) 相比功能较差 (因为无法链式处理多个 `autoloader`)， 而且也无法在两种 `autoloading` 样式中配合使用。

### [create_function()](http://php.net/manual/zh/function.create-function.php) 函数

考虑到此函数的安全隐患问题（它是 [eval()](http://php.net/manual/zh/function.eval.php) 的瘦包装器），该过时的函数现在已被废弃。 更好的选择是[匿名函数](http://php.net/manual/zh/functions.anonymous.php)。

### `(unset)` 类型强制转化

转化任意表达式为此类型，结果总是 `NULL`，所以这个多余的类型转化现在也就被废弃了。

### [parse_str()](http://php.net/manual/zh/function.parse-str.php) 不加第二个参数

使用 [parse_str()](http://php.net/manual/zh/function.parse-str.php) 时，不加第二个参数会导致查询字符串参数导入当前符号表。 考虑到安全隐患问题，不加第二个参数使用 [parse_str()](http://php.net/manual/zh/function.parse-str.php) 的行为已被废弃。 此函数的第二个选项为必填项，它使查询字符串转为 Array。

### [each()](http://php.net/manual/zh/function.each.php) 函数

使用此函数遍历时，比普通的 `foreach` 更慢， 并且给新语法的变化带来实现问题。因此它被废弃了。

### [assert()](http://php.net/manual/zh/function.assert.php) 一个字符串参数

[assert()](http://php.net/manual/zh/function.assert.php) 字符串参数将要求它能被 [eval()](http://php.net/manual/zh/function.eval.php) 执行。 考虑到可能被执行远程代码，废弃了字符串的 [assert()](http://php.net/manual/zh/function.assert.php)，最好提供 `bool` 的表达式。

----

**原文**

- 新特性：http://php.net/manual/zh/migration72.new-features.php
- 废弃的功能：http://php.net/manual/zh/migration72.deprecated.php