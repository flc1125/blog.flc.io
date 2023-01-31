----
title: 有度 Golang 版 SDK
date: 2022-07-07 08:54:58
cover: https://s.flc.io/202301310907195.png
categories: 编程
tags: 
- Go
- SDK
- Youdu
----

## 地址

[github.com/go-packagist/youdu](https://github.com/go-packagist/youdu)

## 安装

```bash
go get github.com/go-packagist/youdu
```

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
