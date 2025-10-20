----
title: "屏蔽 MacOS 的系统升级"
cover: https://s.flc.io/202510201324449.png!blog
date: 2025-10-20
categories: 分享
tags:
- MacOS
----

## 网络层阻断

向 `/etc/hosts` 文件添加以下内容：

```
127.0.0.1 swscan.apple.com
127.0.0.1 swcdn.apple.com
127.0.0.1 swquery.apple.com
127.0.0.1 swdownload.apple.com
127.0.0.1 swdist.apple.com
```

<!-- more -->

保存后，刷新 DNS 缓存：

```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

然后就可以看到：

![](https://s.flc.io/202510201325418.png)

## 一些其他方法

By ChatGPT: https://chatgpt.com/s/t_68f5c829432881918d0a11a86f6647ac
