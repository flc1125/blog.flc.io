----
title: Swoole 协程的思考-执行耗时
date: 2020-08-21 10:52
categories: [后端]
tags: 
- PHP
- Swoole
toc: true
----

## 测试

### 情况一：常规执行

```php
for ($i = 0; $i < 4; $i ++) {
    sleep(2);
    echo $i.'::'.microtime(true).PHP_EOL;
}
echo 'end...';
```

<!-- more -->

输出

```
root@dd42237ba4ed:/data/www/demo/php/swoole/co# time php time.php
0::1595599681.4748
1::1595599683.4801
2::1595599685.4831
3::1595599687.4845
end...

real	0m8.185s
user	0m0.081s
sys	0m0.076s
```

### 情况二：单个协程执行，阻塞

```php
go(function() {
    for ($i = 0; $i < 4; $i ++) {
        sleep(2);
        echo $i.'::'.microtime(true).PHP_EOL;
    }
});
echo 'end...';
```

输出

```
0::1595599971.73
1::1595599973.7313
2::1595599975.7316
3::1595599977.7331
end...

real	0m8.054s
user	0m0.023s
sys	0m0.021s
```

### 情况三：单个协程执行，非阻塞

```php
go(function() {
    for ($i = 0; $i < 4; $i ++) {
        Co::sleep(2);
        echo $i.'::'.microtime(true).PHP_EOL;
    }
});
echo 'end...'.PHP_EOL;

```

输出

```php
end...
0::1595600113.2687
1::1595600115.2422
2::1595600117.2496
3::1595600119.2546

real	0m8.041s
user	0m0.023s
sys	0m0.016s
```

### 情况四：多个协程执行，阻塞

```php
for ($i = 0; $i < 4; $i ++) {
    go(function() use ($i) {
        sleep(2);
        echo $i.'::'.microtime(true).PHP_EOL;
    });
}
echo 'end...';
```

输出

```
0::1595600362.917
1::1595600364.9232
2::1595600366.9272
3::1595600368.9314
end...

real	0m8.067s
user	0m0.024s
sys	0m0.020s
```

### 情况五：多个协程执行，非阻塞

```php
for ($i = 0; $i < 4; $i ++) {
    go(function() use ($i) {
        Co::sleep(2);
        echo $i.'::'.microtime(true).PHP_EOL;
    });
}
echo 'end...'.PHP_EOL;
```

输出

```bash
end...
0::1595600435.1706
3::1595600435.1709
2::1595600435.1711
1::1595600435.1711

real	0m2.053s # 约 2 s
user	0m0.019s
sys	0m0.021s
```

## 总结

- 单个协程的与常规处理性能无太大差异
- 在多个协程下，性能提升更明显
- 在多个协程下，需内部为非阻塞代码，性能提升才有效；否则与常规性能无异
- 个人理解（参考）：
   - 多个协程下，如非阻塞逻辑，协程会让出控制权，但不影响非阻塞代码的执行
- 多协程下处理下，各协程的处理顺序不可控

## 参考文档

- 综合解释swoole的协程原理：[https://blog.csdn.net/qq_16059847/article/details/90792888](https://blog.csdn.net/qq_16059847/article/details/90792888)
