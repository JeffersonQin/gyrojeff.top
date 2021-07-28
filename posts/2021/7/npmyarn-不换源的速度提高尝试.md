---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-07-28 21:29:25'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-28 21:29:25'
parent: 0
password: ''
slug: npmyarn-不换源的速度提高尝试
status: publish
tags: []
template: ''
title: npm/yarn 不换源的速度提高尝试
type: post
---
今天在折腾`vue-electron`的时候遇到了些问题，特此记录。

众所周知，`npm`是可以换源的，但有的时候不可避免，在安装的时候还会碰到境外的下载内容（在安装脚本中），这个时候我们需要其他办法来提高速度。

注意：本文中的所有办法都是基于有代理服务器的情况。如果您不知道什么是代理服务器，建议google。举一个生动形象的例子：酸酸乳（SSR）的“直连模式”就是在本地搭建代理服务器，转发流量。

# 方法1 - `proxychains`

这个是老生常谈了，我们就不多做展开了。命令示例（可能和我不一样，有人用`ng`版本）：

```bash
proxychains4 npm install
```

```bash
proxychains4 yarn
```

# 方法2 - `bash`的`proxy`

通过设置`bash`的环境变量的`proxy`来达到加速的效果：

```bash
http_proxy="<proxy_address>" https_proxy="<proxy_address>" npm install
```

```bash
http_proxy="<proxy_address>" https_proxy="<proxy_address>" yarn
```

# 方法3 - `npm`的`proxy`

通过设置`npm`的`proxy`来达到加速的效果：

```bash
npm config set proxy=http://<server>:<port>
npm config set https_proxy=http://<server>:<port>
```

如果有密码：

```bash
npm config set proxy http://<username>:<password>@<server>:<port>
npm confit set https-proxy http://<username>:<password>@<server>:<port>
```

注意：这个是永久性设置。如果要删除，使用如下命令：

```bash
npm config delete proxy
npm config delete https-proxy
```

安装一来是正常使用命令即可：

```bash
npm install
```

```bash
yarn
```

# Reference

- https://blog.csdn.net/yanzi1225627/article/details/80247758
- https://www.cnblogs.com/yy690486439/p/6688179.html
