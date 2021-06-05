---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-13 12:39:01'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/5.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-13 12:39:01'
parent: 0
password: ''
slug: '225'
status: publish
tags: [Hexo, JS]
template: ''
title: Hexo速度慢之排障 | 一个失效JS引发的惨剧
type: post
---
# Preface

之所以会将博客迁移至Typecho，我给出的理由无外乎两个：一个是LaTeX的解析像是中彩票，另一个就是无法容忍的速度之慢。然而，好像是我的错（悲

# 起因

我是一个恋旧的人，Hexo的博客虽然不再维护，却还是写了一整套维护文档，换了一个不那么显眼的域名，继续适配。上次正好有个东西需要用到旧博客查看，然而事情却出乎了我的意料（

我当时打开了浏览器的Inspector，然后发现某一个请求的时间极长：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610512479.png)

我回到Hexo的项目中定睛一看。好，我的锅，这两个无效链接，导致速度一下子变慢

# 解决办法

换掉所有设置中的JS链接，全部使用jsdelivr的脚本（速度好评！加上Cloudflare的加持，这速度能慢？

不过博客是不会迁回去了，毕竟有后端的博客用过就回不去了（苦涩。



