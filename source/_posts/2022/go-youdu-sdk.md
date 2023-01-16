----
title: 有度 Golang 版 SDK
date: 2022-07-07 08:54:58
categories: [编程,Go]
tags: 
- Go
- SDK
----

## 地址

[github.com/go-packagist/youdu](https://github.com/go-packagist/youdu)

## 安装

```bash
go get github.com/go-packagist/youdu
```

!!! note Hexo-admonition 插件使用示例
    这是基于 hexo-admonition 插件渲染的一条提示信息。类型为 note，并设置了自定义标题。

    提示内容开头留 4 个空格，可以有多行，最后用空行结束此标记。


<!-- more -->

## 使用

```go
package main

import (
    "github.com/go-packagist/youdu"
    "github.com/go-packagist/youdu/message"
    "log"
)

func main() {
    yd := youdu.New(&youdu.Config{
        Api:    "http://domain.com/api",
        Buin:   1111111,
        AppId:  "22222222222222",
        AesKey: "3444444444444444444444444444444444",
    })

    yd.Message().SendText("11111", "test")
    yd.Message().Send(&message.TextMessage{
        ToUser:  "11111",
        ToDept:  "",
        MsgType: message.MsgTypeText,
        Text: &message.TextItem{
            Content: "test",
        },
    })

    mediaId, err := yd.Media().Upload(youdu.MediaTypeImage, "test.jpeg")
    if err != nil {
        panic(err)
    }
    yd.Message().Send(&message.ImageMessage{
        ToUser:  "11111",
        ToDept:  "",
        MsgType: message.MsgTypeImage,
        Image: &message.MediaItem{
            MediaId: mediaId,
        },
    })

}
```

## 参考资料

- 有度 API 官方文档：(youdu.im/doc/api/c01_00001.html)[https://youdu.im/doc/api/c01_00001.html]
