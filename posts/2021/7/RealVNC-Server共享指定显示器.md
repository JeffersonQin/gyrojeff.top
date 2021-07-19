---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-07-19 23:58:02'
modified: '2021-07-19 23:58:02'
parent: 0
password: ''
slug: RealVNC-Server共享指定显示器
status: publish
tags: []
template: ''
title: RealVNC Server共享指定显示器
type: post
---
最近想在床上推Gal, 不想坐在桌前，就给Surface Pro X整了个VNC，但是RealVNC会共享所有屏幕，需要解决这个问题。

方法：在`Server`端进行设置：
- `Options -> Expert -> DisplayDevice`

我的值: `\\.\Display1`, 官方`doc`: https://help.realvnc.com/hc/en-us/articles/360002251297-VNC-Server-Parameter-Reference-#server-displaydevice

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210720000158.png)
