----
title: Swoole 协程的思考-执行顺序
date: 2020-07-24 22:08
categories: [编程,后端]
tags: 
- PHP
- Swoole
toc: true
----

## 示例

### 情况一：非阻塞IO

```php
<?php

go(function () {
    echo 'AA'.date('Y-m-d H:i:s').PHP_EOL;
    Co::sleep(1);
    echo 'BBB'.date('Y-m-d H:i:s').PHP_EOL;
});

go(function () {
    echo 'CC'.date('Y-m-d H:i:s').PHP_EOL;
    echo 'DD'.date('Y-m-d H:i:s').PHP_EOL;
});
```

<!-- more -->

输出

```
AA2020-07-22 17:12:55
CC2020-07-22 17:12:55
DD2020-07-22 17:12:55
BBB2020-07-22 17:12:56
```

### 情况二：阻塞IO

```php
<?php

go(function () {
    echo 'AA'.date('Y-m-d H:i:s').PHP_EOL;
    sleep(1);
    echo 'BBB'.date('Y-m-d H:i:s').PHP_EOL;
});

go(function () {
    echo 'CC'.date('Y-m-d H:i:s').PHP_EOL;
    echo 'DD'.date('Y-m-d H:i:s').PHP_EOL;
});
```

输出

```
AA2020-07-22 17:13:32
BBB2020-07-22 17:13:33
CC2020-07-22 17:13:33
DD2020-07-22 17:13:33
```

### 情况三：前面阻塞IO，后面非阻塞IO

```php
<?php

go(function () {
    echo 'AA'.date('Y-m-d H:i:s').PHP_EOL;
    sleep(1);
    echo 'BBB'.date('Y-m-d H:i:s').PHP_EOL;
});

go(function () {
    echo 'CC'.date('Y-m-d H:i:s').PHP_EOL;
    Co::sleep(2);
    echo 'DD'.date('Y-m-d H:i:s').PHP_EOL;
});
```

```
AA2020-07-22 17:14:53
BBB2020-07-22 17:14:54
CC2020-07-22 17:14:54
DD2020-07-22 17:14:56
```

### 情况四：前面非阻塞IO，后面阻塞IO

```php
<?php

go(function () {
    echo 'AA'.date('Y-m-d H:i:s').PHP_EOL;
    Co::sleep(2);
    echo 'BBB'.date('Y-m-d H:i:s').PHP_EOL;
});

go(function () {
    echo 'CC'.date('Y-m-d H:i:s').PHP_EOL;
    sleep(1);
    echo 'DD'.date('Y-m-d H:i:s').PHP_EOL;
});
```

输出

```php
AA2020-07-22 17:16:39
CC2020-07-22 17:16:39
DD2020-07-22 17:16:40
BBB2020-07-22 17:16:42
```

### 情况五：多个协程运行时

```php
<?php

go(function () {
    echo 'AA'.date('Y-m-d H:i:s').PHP_EOL;
    Co::sleep(2);
    echo 'BBB'.date('Y-m-d H:i:s').PHP_EOL;
});

go(function () {
    echo 'CC'.date('Y-m-d H:i:s').PHP_EOL;
    sleep(1);
    echo 'DD'.date('Y-m-d H:i:s').PHP_EOL;
});

go(function () {
    echo 'EE'.date('Y-m-d H:i:s').PHP_EOL;
    echo 'FF'.date('Y-m-d H:i:s').PHP_EOL;
});
```

输出

```
AA2020-07-22 17:20:17
CC2020-07-22 17:20:17
DD2020-07-22 17:20:18
EE2020-07-22 17:20:18
FF2020-07-22 17:20:18
BBB2020-07-22 17:20:20
```

## 总结

1. 协程如遇到阻塞代码，会跳出当前协程，执行下一个协程。直至处理结束后，继续处理.
1. 协程的处理是串行的，非并行处理
1. 一个进程有多个线程，一个线程有多个协程
1. 进程和线程是系统态；线程是用户态
1. 协程仅是一个函数，不支持多核计算
1. 一个线程内的多个协程虽然可以切换，但是多个协程是串行执行的，只能在一个线程内运行，没法利用CPU多核能力
1. 协程与进程一样，切换是存在上下文切换问题的。

## 参考文档

- 协程介绍：[https://www.jianshu.com/p/6dde7f92951e](https://www.jianshu.com/p/6dde7f92951e)
- Swoole 官方协程文档：[https://wiki.swoole.com/#/coroutine](https://wiki.swoole.com/#/coroutine)
- 课程：[https://coding.imooc.com/lesson/197.html#mid=30612](https://coding.imooc.com/lesson/197.html#mid=30612)



