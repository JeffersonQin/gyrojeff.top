---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-30 22:11:41'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/%E4%BA%AC%E5%90%B9-5.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-30 22:11:41'
parent: 0
password: ''
slug: '275'
status: publish
tags: [Apache, HTTPS, Linux, Ubuntu, 二级域名]
template: ''
title: Ubuntu Apache配置二级域名
type: post
---
# Preface

之前给化学老师做了一个元素周期表的网站，~~虽然是直接从Github上整的~~。当时为了避免麻烦，没有做二级域名，但却引来了其他的麻烦（裂，具体是啥看之前的文章。

所以，为了优雅的解决一系列事情，我把它现在整成了二级域名（超级香！

# 申请SSL证书

一开始我以为腾讯云的免费`SSL`证书只有一个，但是后来发现并不是这样（愉快，申请过程和之前搭建博客的时候一样，所以这里就不放图片了，有需要的去看一下之前搭建博客的文章（（

# 配置Apache

## 添加`site`

到`sites-available`里面，新建一个`.conf`文件，其实名称啥的无所谓，我这里为了方便管理就起了`001-ssl.conf`，接下来看一下配置文件详情：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130214646_e0b22a5e46ae44c231cb1912c06ab0bc.png)

上图的配置文件主要是`default-ssl.conf`的内容上加以更改，需要注意的是就是绿框的内容：

- 第一个绿框内是一些基本信息
  - `ServerName`: 域名
  - `ServerAdmin`：大家看着办
  - `ServerAlias`：其他的域名（会转发到域名）
  - `DocumentRoot`：存放这个二级域名网站内容的文件夹
- 第二个绿框：`log`的存放，大家自己斟酌
- 第三个和第四个绿框：`SSL`证书的位置，具体怎么获取详见之前的文章

配置完之后，我们要让它生效，所以要做一个软链接到`sites-enabled`.

> 注意：软链接要用绝对路径

```bash
ln -s <file> <link>
```

## 在`apache2.conf`里给文件路径赋权

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130220418_87ae36a6bf29b1411768c385fb8f61d9.png)

在`Directory`的后面天上前面设置的`DocumentRoot`的路径即可。

## `http`自动跳转

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130220621_bee61f0b154495c5bb2ddde3e2e0a57f.png)

# 结束语

网上虽然有很多教程，但却一直感觉奇奇怪怪，讲得不明不白。虽然我也只是通过猜测获取大部分信息，但姑且按照自己说得通的逻辑把东西写了出来。

# Reference

- [1] https://www.gworg.com/ssl/255.html
- [2] https://www.cnblogs.com/hzb462606/p/10682442.html
- [3] https://blog.csdn.net/zmzwll1314/article/details/52662273
- [4] https://blog.csdn.net/msllws/article/details/80996299
- [5] https://blog.csdn.net/alps1992/article/details/49183747
  

