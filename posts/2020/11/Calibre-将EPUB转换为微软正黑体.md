---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [记录]
created: '2020-11-27 20:01:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'yes', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/50.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:54'
parent: 0
password: ''
slug: '68'
status: publish
tags: [Calibre, EPUB, JHengHei, ttc, ttf, 字体, 微软正黑体, 电子书]
template: ''
title: Calibre | 将EPUB转换为微软正黑体
type: post
---
# Preface

这两天正好在补魔劣的小说，在手机上是拿iBook看的，到电脑上用的是Calibre。iBook的阅读体验可谓是极佳的，这让我无比舒适，但是Calibre上阅读时显示的是宋体，长时间阅读让眼睛酸痛不已，为了解决这个问题，我找到了微软正黑体这个字体（Microsoft JHengHei）

# 方法

## 获得ttf文件

由于Calibre转换时必须要使用`ttf`文件而不能使用`ttc`文件，所以我们首先需要转换。前往`Windows/Fonts`文件夹（系统盘），找到`Microsoft JHengHei`把它复制出来：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1606477980.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1606478013.png)

随便找个工具`ttc2ttf`（直接百度）然后获得`ttf`文件

## Calibre转换书籍

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1606478065.png)

转换书籍 - 界面外观

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1606478146.png)

添加前面下载的字体，然后选择`嵌入到所有字体`

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1606478102.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1606478229.png)

# p.s. 其实也可以直接调整阅读器的设置

阅读器 -> 右键 -> 首选项 -> 字体

