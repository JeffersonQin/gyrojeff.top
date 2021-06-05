---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-13 16:33:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/22.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-13 22:32:21'
parent: 0
password: ''
slug: '245'
status: publish
tags: [CocoaPods, Xcode]
template: ''
title: CocoaPods安装MobileVLCKit | 加速
type: post
---
# Preface

使用dandanplay的时候和iOS开发聊了一下，于是想自己拿Swift复现一下。播放视频的核心框架是MobileVLCKit，安装时遇到了若干问题，故记录下来

# 安装

主要方法参考官方文档：https://code.videolan.org/videolan/VLCKit

这里只讨论加速安装的问题。如果大家有能力，当然可以使用proxychain之类的工具直接挂梯子，但是非常的繁琐，所以这里我就采用另一种较为简单的方法。

首先，和往常一样，配置好`Podfile`然后：

```bash
pod install --verbose
```

参数解释：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610525501.png)

如果你网速够快，那就没下文的事了。但是如果不然，可以直接ctrl+c终止这个进程了。

首先，进入`~/.cocoapods/repos/trunk/Specs/b/f/7/MobileVLCKit/`，

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610525599.png)

像上面这样，找到你需要安装的版本，然后去查看json文件，找到里面的下载链接：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610525880.png)

如果你懒，你可以直接：

```bash
cat MobileVLCKit.podspec.json | grep http
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610525943.png)

然后用你的**下载工具**（懂得都懂）自行下载。下载完毕之后，我们开一个本地的http server。如果你有`python`环境，可以非常简单的用下面这行命令实现：

```bash
python3 -m http.server 80
```

服务器的根目录就是当前目录。

随后，打开浏览器，输入`127.0.0.1`或者localhost就可以访问。

接下来，将之前json文件中的链接更改为下载文件的本地链接即可。

最后，重新

```bash
pod install --verbose
```

# Reference

- [1] https://blog.csdn.net/jiaxianhua/article/details/96114249

