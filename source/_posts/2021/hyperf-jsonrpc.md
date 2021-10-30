----
title: Hyperf：JSON-RPC 服务
date: 2021-10-30 12:28:51
categories: [编程,PHP]
tags: 
- Hyperf
- JSON-RPC
toc: true
----

## Server 端

### 定义服务(示例）

```php
<?php

declare(strict_types=1);

namespace App\JsonRpc;

use Hyperf\RpcServer\Annotation\RpcService;

/**
 * 注意，如希望通过服务中心来管理服务，需在注解内增加 publishTo 属性.
 * @RpcService(name="TestsService", protocol="jsonrpc-http", server="jsonrpc-http")
 */
class TestsService
{
    // 实现一个加法方法，这里简单的认为参数都是 int 类型
    public function add(int $a, int $b): int
    {
        // 这里是服务方法的具体实现
        return $a + $b;
    }
}
```

<!-- more -->

### 对外提供服务

> 文件：`config\autoload\server.php`

```php
<?php

declare(strict_types=1);

use Hyperf\Server\Event;
use Hyperf\Server\Server;
use Swoole\Constant;

return [
    'mode' => SWOOLE_PROCESS,
    'servers' => [
        // ....
        [
            'name' => 'jsonrpc-http',
            'type' => Server::SERVER_HTTP,
            'host' => '0.0.0.0',
            'port' => 8001,
            'sock_type' => SWOOLE_SOCK_TCP,
            'callbacks' => [
                Event::ON_REQUEST => [\Hyperf\JsonRpc\HttpServer::class, 'onRequest'],
            ],
        ],
      	// ....
    ],
    // ...
];
```

## Client 端

### 配置调用端

> 此处简易版（未使用 Consul）  
> 相关文件：`config\autoload\services.php`

```php
<?php

declare(strict_types=1);

return [
    'consumers' => [
        [
            'name' => 'TestsService',  // 对应前面的服务
            'nodes' => [
                ['host' => '127.0.0.1', 'port' => 8001]  // 对应服务端开放的IP和端口
            ],
        ],
    ],
];
```

### 配置消费端（客户端）

```php
<?php

declare(strict_types=1);

namespace App\JsonRpc;

use Hyperf\RpcClient\AbstractServiceClient;

class TestsConsumer extends AbstractServiceClient
{
    /**
     * 定义对应服务提供者的服务名称.
     * @var string
     */
    protected $serviceName = 'TestsService';

    /**
     * 定义对应服务提供者的服务协议.
     * @var string
     */
    protected $protocol = 'jsonrpc-http';

    public function add(int $a, int $b): int
    {
        return $this->__request(__FUNCTION__, compact('a', 'b'));
    }
}
```

### 调用消费端

```php
<?php
  
(new TestsConsumer($this->container))->add(1, 2);
```

### HTTP 协议：实际的请求信息

**请求地址：**

- http://192.168.2.102:8001/

**请求方式：**

- POST

**请求参数：**

```json
{
    "jsonrpc": "2.0",
    "method": "\/tests\/add",
    "params": {
        "a": 1,
        "b": 2
    },
    "id": "617b67ade764a",
    "context": []
}
```

**返回结果：**

```json
{
    "jsonrpc": "2.0",
    "id": "617b67ade764a",
    "result": 3,
    "context": []
}
```

## 进阶

- 多路复用：[传送门](https://hyperf.wiki/2.2/#/zh-cn/rpc-multiplex)
- 基于 TCP 协议的 JsonRPC
- gRPC
- RequestID：链路跟踪