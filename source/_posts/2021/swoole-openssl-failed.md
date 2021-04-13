----
title: 安装 Swoole 提示 openssl 相关报错处理
date: 2021-4-1 15:47:23
categories: [编程,后端]
tags: 
- Swoole
- PHP
- OpenSSL
toc: true
----

## 报错

```
ocal/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -I/usr/local/openssl/include -I/tmp/pear/temp/swoole -I/tmp/pear/temp/swoole/include -I/tmp/pear/temp/swoole/ext-src -I/tmp/pear/temp/swoole/thirdparty/hiredis -DHAVE_CONFIG_H -g -O2 -Wall -Wno-unused-function -Wno-deprecated -Wno-deprecated-declarations -std=c++11 -c /tmp/pear/temp/swoole/ext-src/php_swoole.cc  -fPIC -DPIC -o ext-src/.libs/php_swoole.o
In file included from /tmp/pear/temp/swoole/include/swoole_socket.h:34,
                 from /tmp/pear/temp/swoole/include/swoole_coroutine.h:22,
                 from /tmp/pear/temp/swoole/ext-src/php_swoole_coroutine.h:22,
                 from /tmp/pear/temp/swoole/ext-src/php_swoole_cxx.h:20,
                 from /tmp/pear/temp/swoole/ext-src/php_swoole.cc:16:
/tmp/pear/temp/swoole/include/swoole_ssl.h:27:10: fatal error: openssl/ssl.h: No such file or directory
 #include <openssl/ssl.h>
          ^~~~~~~~~~~~~~~
compilation terminated.
make: *** [Makefile:192: ext-src/php_swoole.lo] Error 1
ERROR: `make' failed
```

<!-- more -->

## 处理

![](https://s.flc.io/2021-04-01-15-49-24.png)

起初尝试网上方法：`pecl install swoole` 时，出现 openssl 选择 `yes --with-openssl-dir=XXXX` 不可用。

而后尝试查找 swoole 官方 [dockerfile](https://github.com/swoole/docker-swoole/blob/master/dockerfiles/4.6.4/php8.0/cli/Dockerfile) 发现缺乏基础包，安装好后生效

```bash
apt-get install -y             \
        libcurl4-openssl-dev       \
        libssl-dev                 \
        supervisor                 \
        unzip                      \
        zlib1g-dev                 \
        --no-install-recommends
```

因本地使用 docker 启动，在 `docker-php-ext-enable swoole` 即可。

```bash
root@4815e2eb24a2:~# php --ri swoole

swoole

Swoole => enabled
Author => Swoole Team <team@swoole.com>
Version => 4.6.4
Built => Apr  1 2021 07:45:34
coroutine => enabled with boost asm context
epoll => enabled
eventfd => enabled
signalfd => enabled
cpu_affinity => enabled
spinlock => enabled
rwlock => enabled
openssl => OpenSSL 1.1.1d  10 Sep 2019  // ←←←已支持
dtls => enabled
zlib => 1.2.11
mutex_timedlock => enabled
pthread_barrier => enabled
futex => enabled
async_redis => enabled

Directive => Local Value => Master Value
swoole.enable_coroutine => On => On
swoole.enable_library => On => On
swoole.enable_preemptive_scheduler => Off => Off
swoole.display_errors => On => On
swoole.use_shortname => Off => Off
swoole.unixsock_buffer_size => 8388608 => 8388608
```

## 参考文档

- `--with-openssl-dir`：https://developpaper.com/enable-openssl-support-require-openssl-library-appears-when-installing-swoole-on-mac/
- Swoole 官方 Dockfile 文件：https://github.com/swoole/docker-swoole/blob/master/dockerfiles/4.6.4/php8.0/cli/Dockerfile