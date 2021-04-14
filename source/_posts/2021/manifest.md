----
title: Web 应用程序清单 manifest
date: 2021-04-13 23:29:41
updated: 2021-04-14 23:21:30
categories: [编程,前端]
tags: 
- manifest
- PWA
toc: true
----

## 起源

检查博客有一个 http://flc.io/manifest.json 的链接。之前一直未具体研究，所以想做一些简单了解。

当然，除了链接，还发现对应的浏览器控制台：

![](https://s.flc.io/2021-04-13-23-44-43.png)

<!-- more -->

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

> 接下来会简单，介绍常用的几个配置，并描述相关作用。期间包含相关测试结果（仅供参考，可能测试姿势不对）

#### `name`

为应用程序提供一个人类可读的名称，例如：在其他应用程序的列表中或作为图标的标签显示给用户。

```json
{
    "name": "叶子坑"
}
```

#### `short_name`

为应用程序提供简短易读的名称。 这是为了在没有足够空间显示Web应用程序的全名时使用。

```json
{
    "short_name": "叶子坑"
}
```

> 目前 iPad 测试来看，若配置了 `short_name`，会优先于 `name` 的使用。

#### `theme_color`

定义应用程序的默认主题颜色。 这有时会影响操作系统显示应用程序的方式（例如，在 Android 的任务切换器上，主题颜色包围应用程序）。

```json
{
    "theme_color": "aliceblue"
}
```

> iPad 并未发现特别的……

#### `background_color`

为 web 应用程序预定义的背景颜色。此值在应用程序样式表中可以再次声明。它主要用于在样式表加载之前，当加载 manifest，浏览器可以用来绘制 web 应用程序的背景颜色。在启动web应用程序和加载应用程序的内容之间创建了一个平滑的过渡。

**注意：** `background_color` 只是在web应用程序加载时提高用户体验，而当 web 应用程序的样式表可用时，不能替代作为背景颜色使用。

> 起初的理解是，设置该颜色值后，进入 Web App 时，在样式未加载前的背景底色。但 iPad 测试，这个理解有误。

#### `description`

提供有关 Web 应用程序的一般描述。

#### `display`

定义开发人员对 Web 应用程序的首选显示模式。

**有效值:**

| 显示模式       | 描述                                                                                          | 后备显示模式     |
|------------|---------------------------------------------------------------------------------------------|------------|
| fullscreen | 全屏显示, 所有可用的显示区域都被使用, 并且不显示状态栏chrome。                                                        | standalone |
| standalone | 让这个应用看起来像一个独立的应用程序，包括具有不同的窗口，在应用程序启动器中拥有自己的图标等。这个模式中，用户代理将移除用于控制导航的UI元素，但是可以包括其他UI元素，例如状态栏。 | minimal-ui |
| minimal-ui | 该应用程序将看起来像一个独立的应用程序，但会有浏览器地址栏。 样式因浏览器而异。                                                    | browser    |
| browser    | 该应用程序在传统的浏览器标签或新窗口中打开，具体实现取决于浏览器和平台。 这是默认的设置。                                               | (None)     |

#### `icons`

指定可在各种环境中用作应用程序图标的图像对象数组。 例如，它们可以用来在其他应用程序列表中表示Web应用程序，或者将Web应用程序与OS的任务切换器和/或系统偏好集成在一起。

```json
{
    "icons": [
        {
            "src": "icon/lowres.webp",
            "sizes": "48x48",
            "type": "image/webp"
        },
        {
            "src": "icon/lowres",
            "sizes": "48x48"
        },
        {
            "src": "icon/hd_hi.ico",
            "sizes": "72x72 96x96 128x128 256x256"
        },
        {
            "src": "icon/hd_hi.svg",
            "sizes": "72x72"
        }
    ]
}
```

**图像对象可能包含以下值：**

| 字段    | 描述                                           |
|-------|----------------------------------------------|
| sizes | 包含空格分隔的图像尺寸的字符串。                             |
| src   | 图像文件的路径。 如果src是一个相对URL，则基本URL将是manifest的URL。 |
| type  | 提示图像的媒体类型。此字段的目的是允许用户代理快速忽略不支持的媒体类型的图像。      |


> 实际测试：桌面的 App 图标配置。网上查的资料是：应对不同的系统、场景，所使用的的图标尺寸不同。

#### `start_url`

指定用户从设备启动应用程序时加载的 URL。 如果以相对URL的形式给出，则基本 URL 将是 manifest 的 URL。

```json
{
    "start_url": "./?utm_source=web_app_manifest"
}
```

> 用于区分流量不错

### 效果

**通过 Safari 添加到主屏幕的效果：**

![添加主屏幕](https://s.flc.io/2021-04-14-22-33-29.jpeg)

![Web App 应用 - 桌面显示](https://s.flc.io/2021-04-14-22-33-39.jpeg)

![点击 App 图标界面（与浏览器使用有一定的区别：无地址栏）](https://s.flc.io/2021-04-14-22-45-56.jpeg)

**通过 Safari 添加到个人收藏**

![图标、名称](https://s.flc.io/2021-04-14-23-19-08.jpeg)

**通过 Google Chrome 添加到书签**

![图标、名称](https://s.flc.io/2021-04-14-23-19-57.jpeg)

## 参考文档

- [MDN：Web App Manifest](https://developer.mozilla.org/zh-CN/docs/Web/Manifest)
- [PWA Manifest图标生成工具](https://www.mk2048.com/blog/blog_h1caaac22ji1j.html)