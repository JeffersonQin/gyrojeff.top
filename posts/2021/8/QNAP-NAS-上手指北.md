---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-19 17:15:50'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-19 17:15:50'
parent: 0
password: ''
slug: QNAP-NAS-上手指北
status: publish
tags: [NAS, QNAP, 网络]
template: ''
title: QNAP NAS 上手指北
type: post
---
家里有囤积照片的需求，于是乎就入了QNAP的NAS，这个型号：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210820231057.png)

之前刚刚检修过网络（~~其实并没什么用~~）, 下图为家中的网络拓扑

```
光猫
=> AX3600 => QNAP + 我的台式 + 其他无线设备
=> 电力猫 => 其他无线设备
```

# 设置！

大部分内容请参考：https://post.smzdm.com/p/a3gw7q27/

# 关于存储池、卷、共享文件夹、用户组

推荐策略：
- 首先定好RAID方案
- 将一组一组的RAID方案建立存储池
- 每个存储池建立**一个**储存容量占满的**厚卷**
- 在卷中建立**若干个**共享文件夹
- 共享文件夹可以设置**每个用户的权限**

# 关于DDNS

可以用官方的，可以用自己的，我喜欢用自己的。注意：一个域名可以添加多条记录，可以兼顾内网和外网。之后会专门写一篇文章讨论这个问题。

# 配置Qbittorrent

- 使用第三方源
- 教程
  - https://sspai.com/post/57481#
  - https://post.smzdm.com/p/a78z5z9o/

教程不一定要全部遵守，qbit可以按照自己喜欢的方式配置，记得路由器开启uPnP以及开启穿透。

# 关于QSync

千万不要放很多小文件！千万不要放很多小文件！千万不要放很多小文件！

千万不要放正在跑的程序！千万不要放正在跑的程序！千万不要放正在跑的程序！

只能把它当作一个自动同步的仓库！只能把它当作一个自动同步的仓库！只能把它当作一个自动同步的仓库！

# Reference

- https://post.smzdm.com/p/a3gw7q27/
- https://post.smzdm.com/p/aek82wvq/
