---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-06-20 23:12:33'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-06-20 23:12:33'
parent: 0
password: ''
slug: 坑-WSL-Ubuntu-开启-SSH
status: publish
tags: [Linux, SSH, Ubuntu, WSL, 放火墙]
template: ''
title: 坑 | WSL Ubuntu 开启 SSH
type: post
---
# 引言

在做`project-nichijou`，需要连接到`WSL`本地测试服务器看看情况，但是遇到了一些状况：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210620231425.png)

# 解决方案

首先，关于开启`ssh`，无非以下几点：
1. 安装好`ssh`服务器（已经自带了）
2. 配置`/etc/ssh/sshd_config`
   1. `Port` - 端口
   2. `PasswordAuthentication yes` - 用于密码登录

在这之后仍然产生了问题，最终发现是Windows防火墙导致的。

解决：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210620231822.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210620231930.png)

`TCP`协议，端口号即为前面设置的`Port`。

最后使用`HeidiSQL`测试一下：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210620232036.png)

连接成功。
