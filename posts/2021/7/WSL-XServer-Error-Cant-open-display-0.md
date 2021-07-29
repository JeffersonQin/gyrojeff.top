---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-07-29 16:19:37'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-29 18:43:39'
parent: 0
password: ''
slug: WSL-XServer-Error-Cant-open-display-0
status: publish
tags: [GUI, Linux, WSL, XServer]
template: ''
title: 'WSL XServer: Error: Can''t open display: :0'
type: post
---

诸如下面的情况：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/522e30f0b33e017a6b0802f3fcc17fa.png)

经过一天的漫长摸索，终于发现可能是`VcXsrv`抽风。遇到这种情况，我们将鼠标移动到`X Launcher`的托盘图标上：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729162129.png)

`$DISPLAY`设为红框内容即可。例如：

```
export DISPLAY="HQWORKSTATION:40.0"
```

注：`WSL2`的话`HQWORKSTATION`之类的主机名要改为**局域网IP**地址

测试：

```
xcalc
```
