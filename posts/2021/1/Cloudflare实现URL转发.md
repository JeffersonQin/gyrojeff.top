---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-13 12:13:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/3.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-13 12:15:31'
parent: 0
password: ''
slug: '223'
status: publish
tags: [Cloudflare, DNS]
template: ''
title: Cloudflare实现URL转发
type: post
---
# Preface

使用腾讯云的DNSPod的时候发现有URL转发这种解析方法，之后便想把之前买的[gyrojeff.moe](https://gyrojeff.moe)的域名也跳转到这个网站。然而不幸的是，Cloudflare原生并不支持URL解析，遂我们需要想一些办法

# 实现

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610510865.png)

把想要解析的网址随便填一个可靠的IP地址（我这里填的8.8.8.8是Google的DNS，也可以选择别的）

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610510930.png)

然后像上面那样创建两个页面规则即可。301转发是显性转发，即直接跳转过去，302转发是隐性转发，即嵌套一个iframe

# 后记

在等待生效的过程当中，各位相比一定非常焦急。所以大家可以使用`nslookup`命令来查找当前DNS下这个解析是否生效（（

若各位觉得等待时间过久，那么可以更改TLS，让其刷新时间缩短。

