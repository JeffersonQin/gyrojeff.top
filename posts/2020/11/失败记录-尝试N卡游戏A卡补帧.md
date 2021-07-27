---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [记录]
created: '2020-11-18 00:55:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/39.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:27'
parent: 0
password: ''
slug: '28'
status: publish
tags: [AMD, AMD Fluid Motion, Nvidia, 整活, 显卡, 补帧]
template: ''
title: '[失败记录] 尝试N卡游戏A卡补帧'
type: post
---
# Preface

因为一直想要体验AMD Fluid Motion带来的补帧效果，趁着这次FDU发的几百块经费，入了一张二手讯景RX 560。

# 显卡图！

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/02cc30f45219293bcf83d7d34a6e392.jpg)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605632442.jpg)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605632492.jpg)

# 过程

过程其实也非常糟心。最开始我发现，厂家给的电源线过于短小（10cm左右），然后就淘宝买了PCI-E的线。等到了之后发现这个机箱的脑瘫设计，PCI-E的线非常难插（已经插了两根PCI-E给RTX 2070s），所以后来咬了咬牙把2070s的一根PCI-E上的另外一个口给了RX 560.

安装图：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605632614.jpg)

但是，很明显，风道消失了（！（震惊脸）

不过抱着作死的心态，我还是继续安装了驱动和Bluesky FRC。

首先是压力测试：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605632674.jpg)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605632702.jpg)

然后按照常规安装了Bluesky FRC以及配置了PotPlayer的滤镜。

然而：

# 出现的问题（我直接裂开）

- A卡如果不视频输出补帧是没有效果的：
  ![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605632775.jpg)
- A卡视频输出之后和N卡一起运作系统 卡 爆
- 接视频输出五分钟后死机，自动重启
- 以及，看到前面甜甜圈的图，明显的降频（悲

