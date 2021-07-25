---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-07-25 21:06:18'
modified: '2021-07-25 21:06:18'
parent: 0
password: ''
slug: Golang-包管理被墙的解决方案
status: publish
tags: []
template: ''
title: Golang 包管理被墙的解决方案
type: post
---

`go get`不仅不能正常访问，还不支持`proxychains4`，经过一番搜索，找到了如下的解决方案：

```
https_proxy="xxx.xxx.xxx.xxx:xxx" go get <command>
```

或者

```
export https_proxy="xxx.xxx.xxx.xxx:xxx" && go get <command>
```

对于某些软件界面操作，比方说`VS Code`插件的自动安装问题：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210725211618.png)

我们可以直接操作`~/.bashrc`

在最后加上一行：

```
export https_proxy="xxx.xxx.xxx.xxx:xxx"
```

然后

```
source ~/.bashrc
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210725211800.png)

注意：查阅到有人说`http_proxy`是可以的，但是我这里只有`https_proxy`可以，原因不明。

Reference: 
- [天朝局域网内go get的正确姿势](https://blog.scnace.me/%E4%B8%BAgo%20get%E6%8A%A4%E8%88%AA%20/)
- [go get 命令被墙问题](https://blog.csdn.net/ys5773477/article/details/73929161)

