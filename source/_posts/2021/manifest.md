----
title: Web 应用程序清单 manifest[未完结]
date: 2021-04-13 23:29:41
categories: 编程
tags: 
- manifest
- PWA
toc: true
----

## 起源

检查博客有一个 http://flc.io/manifest.json 的链接。之前一直未具体研究，所以想做一些简单了解。

当然，除了链接，还发现对应的浏览器控制台：

![2021-04-13-23-44-43](https://s.flc.io/2021-04-13-23-44-43.png)

## 怎么读？

`manifest`，遇到一个新单词，作为一个正经、有涵养的“英语”课堂，我们先了解下这个单词如何读。

- 中国式英语：门尼费思特……
- 正确读音：[百度翻译](https://fanyi.baidu.com/?aldtype=16047#en/zh/manifest)

    ![](https://s.flc.io/2021-04-13-23-41-10.png)

## 干嘛的？

查了下 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/Manifest) 的相关信息：

> [Web 应用程序清单](https://developer.mozilla.org/zh-CN/docs/Web/web%20app%20manifest)在一个JSON文本文件中提供有关应用程序的信息（如名称，作者，图标和描述）。manifest 的目的是将Web应用程序安装到设备的主屏幕，为用户提供更快的访问和更丰富的体验。
> 
> Web 应用程序清单是被称为[渐进式Web应用程序(PWA)](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps)的Web技术集合的一部分, 它们是可以安装到设备的主屏幕的网络应用程序，而不需要用户通过应用商店，伴随着其他功能, 比如离线可用和接收推送通知。

**简单概述：**

- 一个 JSON 文件；
- 一个提供应用程序信息的文件；
- 可用于将 Web 应用安装到设备的主屏幕，而不依赖于应用商店；
- 是渐进式 Web 应用程序（PWA）的一部分；可用于其他 PWA 功能，如：离线应用、消息通知。

**课外知识：**

> 曾经在做浏览器推送的时候，也曾遇到过这个文件，根据 [Google 官方文档](https://developers.google.com/web/ilt/pwa/introduction-to-push-notifications)，也涉及到该文件的配置。

相关配置示例：

```json
{
    "name": "Push Notifications app",
    "gcm_sender_id": "370072803732"
}
```

## 怎么用？

### 使用

直接在 HTML 里添加如下代码即可：

```html
<link rel="manifest" href="/manifest.json">
```

### 配置

**示例**

```json
{
    "name": "叶子坑",
    "short_name": "Flc",
    "start_url": ".",
    "theme_color": null,
    "background_color": null,
    "display": "standalone",
    "icons": [
        {
            "src": "/assets/avatar.jpg",
            "sizes": "541x541",
            "type": "image/jpg"
        }
    ]
}
```

> 接下来会简单，介绍常用的几个配置，并描述其他作用。

#### `name`

> 待补充

#### `short_name`

> 待补充

#### `theme_color`

> 待补充

#### `background_color`

定义应用背景色。

#### `description`

> 待补充

#### `display`

> 待补充

#### `icons`

> 待补充

#### `orientation`

> 待补充

#### `start_url`

> 待补充

## 我干了啥？

## 参考文档

- [MDN：Web App Manifest](https://developer.mozilla.org/zh-CN/docs/Web/Manifest)