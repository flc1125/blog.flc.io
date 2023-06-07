----
title: 【架构设计】Websocket 消息格式之系统事件
cover: https://s.flc.io/202306070858619.png!blog
date: 2023-06-07 08:56:37
categories: 
    - 编程
toc: true
----

> 封面来源网络，如有侵权，请联系删除。

## 命名规范

系统事件的命名以 `socket:` 为前缀，例如 `socket:connect` 事件。

## 事件列表

### WSS 连接成功 `socket:connected`

- 事件发送方：`服务端`
- 事件信息：

    ```json
    {
        "event": "socket:connected",
        "data": {
            "socket_id": "1ba1b6cb-0127-48f8-b5a5-9fd79a7c68a6",  // socket 唯一ID
            "heart": {
                "interval": 30,  // 心跳间隔，单位：秒（用户客户端发送心跳事件）
                "timeout": 25   // 心跳超时，单位：秒（用于客户端接收心跳事件）
            }
        }
    }
    ```
  
<!-- more -->

### 客户端订阅频道 `socket:subscribe`

- 事件发送方：`客户端`
- 事件信息：

    ```json
    {
        "event": "socket:subscribe",
        "data": {
            "channel": "user:13",  // 需要订阅的频道，其中私有频道格式：private-
            "auth": "XXXXX"  // 私有频道的授权信息
        }
    }
    ```

**频道说明：**

- 公有频道：默认频道均为公有频道，即：客户端可随时订阅
- 私有频道：频道名称以 `private-`为前缀，如：`private-user.1`；其中私有频道必须包含 `auth` 授权信息（该信息请求业务服务端获取）

> **说明：** `auth` 一般包含 `socket_id` 与 `channel` 及业务自定义的其他信息组合后生成。需要保证业务端与 websocket 服务端逻辑一致。

### 频道订阅成功 `socket:subscribed`

- 事件发送方：`服务端`
- 事件信息：

    ```json
    {
        "event": "socket:subscribed"
    }
    ```
  
### 频道订阅失败 `socket:subscribe.error`

- 事件发送方：`服务端`
- 事件信息：

    ```json
    {
        "event": "socket:subscribe.error",
        "data": {
            "error": "the connection is already subscribed to the channel" // 失败原因
        }
    }
    ```
  
### 客户端取消订阅频道 `socket:unsubscribe`

- 事件发送方：`客户端`
- 事件信息：

    ```json
    {
        "event": "socket:unsubscribe",
        "data": {
            "channel": "user:13"  // 需要取消订阅的频道
        }
    }
    ```

### 取消订阅频道成功 `socket:unsubscribed`

- 事件发送方：`服务端`
- 事件信息：

    ```json
    {
        "event": "socket:unsubscribed"
    }
    ```
  
### 取消订阅频道失败 `socket:unsubscribe.error`

- 事件发送方：`服务端`
- 事件信息：

    ```json
    {
        "event": "socket:unsubscribe.error",
        "data": {
            "error": "the connection is not subscribed to the channel" // 失败原因
        }
    }
    ```

### 客户端发送心跳检测 `socket:ping`

- 事件发送方：`客户端`
- 事件信息：

    ```json
    {
        "event": "socket:ping",
        "data": {
            "id": 1 // ping 表示，一般可使用自增ID
        }
    }
    ```

**心跳检测说明：**

- **默认设置：**SDK 设置默认轮训间隔：120 秒；接受 pong 的超时时间：30 秒
- **Socket 设置：**Wss 连接成功后，Socket 服务端会发送 heart 信息，包含了轮训间隔与超时时间，若存在此值，则以该值为准

### 服务端发送心跳回复 `socket:pong`

- 事件发送方：`服务端`
- 事件信息：

    ```json
    {
        "event": "socket:pong",
        "data": {
            "reply": 1  // 回应 ping 发起的 ID
        }
    }
    ```
  
### 服务端发送心跳检测 `socket:ping`

> 该功能可选

- 事件发送方：`服务端`
- 事件信息：

    ```json
    {
        "event": "socket:ping"
    }
    ```

**心跳检测说明：**

- 服务端每 **30 秒**发送一次 `ping`；
- 客户端必须 **25 秒**以内回复 `pong`（参考下文）；否则服务端会自动断开 socket 连接

### 客户端发送心跳回复 `socket:pong`

> 该功能可选

- 事件发送方：`客户端`
- 事件信息：

    ```json
    {
        "event": "socket:pong"
    }
    ```
