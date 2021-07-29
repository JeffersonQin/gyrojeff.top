---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-07-29 22:53:56'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729225826.png',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-29 22:53:56'
parent: 0
password: ''
slug: WSL-12-Hyper-V-安卓模拟器-VMWare-共存方案
status: publish
tags: [Linux, WSL, Windows]
template: ''
title: 'WSL 1/2; Hyper-V; 安卓模拟器; VMWare: 共存方案'
type: post
---
# 已知

- Mumu等市面上一众安卓模拟器是通过魔改VirtualBox实现的，与Hyper-V / WSL2冲突
- VMWare可以与Hyper-V兼容
- WSL 1/2可以共存，WSL 1是将POSIX的调用翻译为NT内核的调用，WSL 2则是直接基于Hyper-V的虚拟机，所以两者可以共存

# 方案

- BlueStacks for Hyper-V
- WSL 1/2
- VMWare
- Hyper-V

完美解决问题。

在此之上我们甚至可以加上：

- Windows Sandbox (基于Hyper-V)
- Docker (for WSL 2)
- ...
