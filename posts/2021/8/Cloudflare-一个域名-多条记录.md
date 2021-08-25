---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-21 20:05:06'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-21 20:05:06'
parent: 0
password: ''
slug: Cloudflare-一个域名-多条记录
status: publish
tags: [网络]
template: ''
title: Cloudflare 一个域名 多条记录
type: post
---
# Preface

入了`NAS`，内网公网想要使用同一个域名访问，于是就有了这个想法。 

# 做法

如下图

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210822214905.png)

# 关于 DDNS

去年我写了 `Cloudflare` 的一个有 `GUI` 的 `py` 的 `DDNS` 工具。借这个机会正好翻修了一下。注意：局域网或者一些特殊的 `IP` 地址，诸如：

- `192.168.*.*`
- `169.254.*.*`

是属于 `reserved IP` ，在 `DDNS` 更新记录的时候不会被覆盖。

# 我吹爆 `Cloudflare`

垃圾 `DNSPod` ，我要交钱才能添加三条以上的 `A` 记录。

# 关于 RDP Android 失效问题

我不知道 `RD Client` 安卓版本是怎么实现的，但是感觉，要么它做了 `Cache`，也么它不支持多记录的域名。其他软件如：

- Qbittorrent Remote
- Via

等都是支持的。
