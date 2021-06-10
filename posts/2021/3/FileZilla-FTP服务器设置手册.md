---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-03-01 00:26:07'
modified: '2021-06-10 12:03:07'
parent: 0
password: ''
slug: FileZilla-FTP服务器设置手册
status: publish
tags: []
template: ''
title: FileZilla | FTP服务器设置手册
type: post
---
# 引言

需要在学校里设置FTP, 遂请可靠大鲜贝用FileZilla Server设置了一下, 这里特此记录。

# 步骤

设置线程

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214155620_b54df424e3276190181e0cf3139500d0.png)

设置协议中告诉对方的IP地址

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214155704_17bce2d80f9f43388b83f6dc867db62c.png)

设置MODE Z

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214155739_d39d10727857879447e0033c6aba612a.png)

设置TLS，如果想要从资源管理器连接，可以不勾选“Disallow plain unencrypted FTP”

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214155811_8498fda56842f578b2a5df04d91c493c.png)

Generate证书的方法

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214155902_a2a154e6b6397bfa033694666aeb6d09.png)

Edit --> Groups里，先在右边创建一个Default的Group

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214160029_ec3827080cf8f90f7129c3e09f3e9224.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214160122_cd2c2381acefd379afe8b920c5f5ea37.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214160142_6e59d7c3f97311b6daae4b6a55016c04.png)

Edit --> Users里

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214160254_6191e207f95c9b1561d9a108667d130e.png)

注意上面的Group就选之前的Default Group

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214160228_705c52aadafa50c81897ce9d0e989c1d.png)

设置防火墙

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214163207_a66a80547ced0ac1f92377837198d08a.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214163229_572797ac8796d8a379ba0023b26b83c1.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210214163253_2822809ba0826500f5bdab9eb20285a9.png)
