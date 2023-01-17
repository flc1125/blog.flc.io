----
title: RocketMQ 安装
date: 2022-04-14 16:49:51
categories: 编程
tags:
- 工具
- RocketMQ
- 消息队列
----

> 此处基于 Macbook Pro 的安装演示

## 环境依赖

- 官方文档：https://rocketmq.apache.org/docs/quick-start/
- JDK
- Maven

### 安装 JDK

- 下载地址：https://www.oracle.com/java/technologies/downloads/#java8-mac
- ![](https://s.flc.io/2022-04-14-16-48-58.png)
- 配置环境变量

    ```bash
    vim ~/.bash_profile
    ```

    输入以下内容：

    ```
    # Java
    export JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_321.jdk/Contents/Home"
    export PATH=$JAVA_HOME/bin:$PATH
    ```

    生效：

    ```bash
    source ~/.bash_profile
    ```

<!-- more -->

### 安装 Maven

```bash
brew install maven
```

## 安装 RocketMQ

### 解压构建

```bash
unzip rocketmq-all-4.9.3-source-release.zip
cd rocketmq-all-4.9.3/
mvn -Prelease-all -DskipTests clean install -U
cd distribution/target/rocketmq-4.9.3/rocketmq-4.9.3
```

mvn 完以后的效果：

```
[INFO] Installing /Users/flc/Downloads/rocketmq-all-4.9.3/distribution/target/rocketmq-4.9.3.zip to /Users/flc/.m2/repository/org/apache/rocketmq/rocketmq-distribution/4.9.3/rocketmq-distribution-4.9.3.zip
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for Apache RocketMQ 4.9.3 4.9.3:
[INFO]
[INFO] Apache RocketMQ 4.9.3 .............................. SUCCESS [  5.822 s]
[INFO] rocketmq-logging 4.9.3 ............................. SUCCESS [  5.700 s]
[INFO] rocketmq-remoting 4.9.3 ............................ SUCCESS [  5.009 s]
[INFO] rocketmq-common 4.9.3 .............................. SUCCESS [  6.565 s]
[INFO] rocketmq-client 4.9.3 .............................. SUCCESS [  5.969 s]
[INFO] rocketmq-store 4.9.3 ............................... SUCCESS [ 17.957 s]
[INFO] rocketmq-srvutil 4.9.3 ............................. SUCCESS [  2.027 s]
[INFO] rocketmq-filter 4.9.3 .............................. SUCCESS [ 28.168 s]
[INFO] rocketmq-acl 4.9.3 ................................. SUCCESS [  6.130 s]
[INFO] rocketmq-broker 4.9.3 .............................. SUCCESS [ 55.353 s]
[INFO] rocketmq-tools 4.9.3 ............................... SUCCESS [  3.923 s]
[INFO] rocketmq-namesrv 4.9.3 ............................. SUCCESS [  2.850 s]
[INFO] rocketmq-test 4.9.3 ................................ SUCCESS [ 36.945 s]
[INFO] rocketmq-openmessaging 4.9.3 ....................... SUCCESS [  2.970 s]
[INFO] rocketmq-example 4.9.3 ............................. SUCCESS [01:00 min]
[INFO] rocketmq-distribution 4.9.3 ........................ SUCCESS [05:09 min]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  09:15 min
[INFO] Finished at: 2022-04-14T16:52:36+08:00
[INFO] ------------------------------------------------------------------------
```

### Start Name Server

```bash
nohup sh bin/mqnamesrv &
tail -f ~/logs/rocketmqlogs/namesrv.log
# The Name Server boot success...
```

### Start Broker

```bash
nohup sh bin/mqbroker -n localhost:9876 &
tail -f ~/logs/rocketmqlogs/broker.log 
# The broker[%s, 172.30.30.233:10911] boot success...
```

> rocketmq-dashboard 留意 IP，否则无法访问。`nohup sh bin/mqbroker -n 0.0.0.0:9876 &`

### Shutdown Servers

```bash
> sh bin/mqshutdown broker
The mqbroker(36695) is running...
Send shutdown request to mqbroker(36695) OK

> sh bin/mqshutdown namesrv
The mqnamesrv(36664) is running...
Send shutdown request to mqnamesrv(36664) OK
```

## 安装 rocketmq-dashboard

```bash
docker run -d --name rocketmq-dashboard -e "JAVA_OPTS=-Drocketmq.namesrv.addr=192.168.2.8:9876" -p 8080:8080 -t apacherocketmq/rocketmq-dashboard:latest
```

> `192.168.2.8` 为服务 IP

## 参考资料

- https://github.com/apache/rocketmq
- https://rocketmq.apache.org/docs/quick-start/
- https://github.com/apache/rocketmq-dashboard

docker run -d --name rocketmq-dashboard -e "JAVA_OPTS=-Drocketmq.namesrv.addr=192.168.2.8:9876" -p 8080:8080 -t apacherocketmq/rocketmq-dashboard:latest