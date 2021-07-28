---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-07-29 01:02:01'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-29 01:02:01'
parent: 0
password: ''
slug: Bluestacks-抓包注入方法-Httpcanary
status: publish
tags: []
template: ''
title: Bluestacks 抓包注入方法 (Httpcanary)
type: post
---
这篇文章其实主要不在讲如何抓包注入，主要讲如何在BlueStacks上装HttpCanary。

众所周知，要想正常使用HttpCanary，我们需要安装CA证书才能不会SSL (https) 的请求。但是，非常可悲的是，BlueStacks的Android是一个被阉割了的Android。每当我们尝试安装CA Certificate的时候，我们就会进入下面这个界面，然后循环往复：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729011901.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729011955.png)

这告诉我，我们是势必要另辟蹊径。

我们不能思考得出如下几条方案：
- 用某种其他方式安装CA证书
- 设置BlueStacks的Proxy，并在Windows下开一个代理服务器，进行抓包注入
- 设置全局的Proxy，开一个代理服务器，解析所有流量，进行抓包注入

长期从事逆向工程的直觉告诉我，第一种方法绝对是最简单的，用其他的办法，后患无穷。

经过一番搜索和可靠大先辈的指引，我发现我们需要`root`，然后即可通过`Root Certificate Manager`即可安装证书。

`root`方法可以参考我的其他文章，这里不多做赘述。我们主要讲如何获取，并安装搞到的证书。所需要用到的工具`Root Certificate Manager`请大家自行搜索下载。

第一步是获取需要安装的证书：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729012535.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729012613.png)

导出之后可以在这里找到：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729012700.png)

第二步：导入。打开`Root Certificate Manager`:

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729012753.png)

文件导入即可。

注意：在导入的过程当中，我们可能找不到文件浏览的入口，后来我也是阴差阳错才找到的，可能需要对某些按钮进行长按或者类似的相关操作。

# Reference

- https://www.51sec.org/2020/11/21/tips-and-tricks-to-run-android-emulator-bluestacks/#Install_Third_Party_CA_Certificate_into_BlueStacks
- https://www.reddit.com/r/BlueStacks/comments/4t402w/help_installing_proxy_ssl_cert_in_bluestacks/
