----
title: Golang 版的 PSR-3 Logger 规范，支持自定义 Logger
date: 2023-03-29 09:52:51
categories: 编程
tags:
- Logger
- Golang
- PSR-3
- Packagist
----

## 开源地址

- https://github.com/go-packagist/logger

|  [![Go Version](https://badgen.net/github/release/go-packagist/logger/stable)](https://github.com/go-packagist/logger/releases) |[![GoDoc](https://pkg.go.dev/badge/github.com/go-packagist/logger)](https://pkg.go.dev/github.com/go-packagist/logger)   |[![codecov](https://codecov.io/gh/go-packagist/logger/branch/master/graph/badge.svg?token=5TWGQ9DIRU)](https://codecov.io/gh/go-packagist/logger)   |[![Go Report Card](https://goreportcard.com/badge/github.com/go-packagist/logger)](https://goreportcard.com/report/github.com/go-packagist/logger)   | [![tests](https://github.com/go-packagist/logger/actions/workflows/go.yml/badge.svg)](https://github.com/go-packagist/logger/actions/workflows/go.yml)  | [![MIT license](https://img.shields.io/badge/license-MIT-brightgreen.svg)](https://opensource.org/licenses/MIT)  |
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------ |

> **说明：** 设计参考 [PHP PSR-3 规范](https://www.php-fig.org/psr/psr-3/)，做了一些调整后实现。

## 安装

```bash
go get github.com/go-packagist/logger
```

## 教程

```go
package main

import (
	"fmt"
	"github.com/go-packagist/logger"
	"time"
)

type CustomLogger struct {
	logger.Loggerable
}

var _ logger.Logger = (*CustomLogger)(nil)

func NewCustomLogger() *CustomLogger {
	c := &CustomLogger{
		Loggerable: func(level logger.Level, s string) {
			fmt.Println(fmt.Sprintf("%s %s: %s", time.Now().Format(time.DateTime), level.UpperString(), s))
		},
	}

	return c
}

func main() {
	c := NewCustomLogger()

	c.Emergencyf("Emergencyf: %s", "test")
	c.Alertf("Alertf: %s", "test")
	c.Criticalf("Criticalf: %s", "test")
	c.Errorf("Errorf: %s", "test")
	c.Warningf("Warningf: %s", "test")
	c.Noticef("Noticef: %s", "test")
	c.Infof("Infof: %s", "test")
	c.Debugf("Debugf: %s", "test")

	c.Emergency("Emergency: test")
	c.Alert("Alert: test")
	c.Critical("Critical: test")
	c.Error("Error: test")
	c.Warning("Warning: test")
	c.Notice("Notice: test")
	c.Info("Info: test")
	c.Debug("Debug: test")

	c.Log(logger.Emergency, "Log: Emergency: test")

	// Output:
	// 2023-03-28 23:18:13 EMERGENCY: Emergencyf: test
	// 2023-03-28 23:18:13 ALERT: Alertf: test
	// 2023-03-28 23:18:13 CRITICAL: Criticalf: test
	// 2023-03-28 23:18:13 ERROR: Errorf: test
	// 2023-03-28 23:18:13 WARNING: Warningf: test
	// 2023-03-28 23:18:13 NOTICE: Noticef: test
	// 2023-03-28 23:18:13 INFO: Infof: test
	// 2023-03-28 23:18:13 DEBUG: Debugf: test
	// 2023-03-28 23:18:13 EMERGENCY: Emergency: test
	// 2023-03-28 23:18:13 ALERT: Alert: test
	// 2023-03-28 23:18:13 CRITICAL: Critical: test
	// 2023-03-28 23:18:13 ERROR: Error: test
	// 2023-03-28 23:18:13 WARNING: Warning: test
	// 2023-03-28 23:18:13 NOTICE: Notice: test
	// 2023-03-28 23:18:13 INFO: Info: test
	// 2023-03-28 23:18:13 DEBUG: Debug: test
	// 2023-03-28 23:18:13 EMERGENCY: Log: Emergency: test
}
```

## 内置 Logger

- `logger.NewNullLogger()` - 啥也不干
- `logger.NewPrintLogger()` - 打印到控制台

## 参考资料

- PSR-3: Logger Interface：https://www.php-fig.org/psr/psr-3/
- PSR Log：https://github.com/php-fig/log
- Go Redis: https://github.com/redis/go-redis/ （参考设计思路）