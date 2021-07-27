---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [记录]
created: '2020-12-14 20:53:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/83.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:56:31'
parent: 0
password: ''
slug: '75'
status: publish
tags: [20H2, Windows]
template: ''
title: 记录 | 20H2关于Alt + Tab的Bug（真的吗？）
type: post
---
# Preface

之前看到别人用20H2，界面好像不太一样，于是便手贱了升级，没想到确实一场噩梦。

先扯点和正文关系不大的事情吧。我其实是一个很喜欢，并且也很重视磁贴的一个人，没成想巨硬竟然会对我的彩色磁贴下手（无能狂怒）。来几张图感受一下吧：

![以前是有背景颜色的](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1607950398.png)

![以前Ubuntu的背景可是橙色的啊！！](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1607950470.png)

# 关于Alt + Tab

一开始，20H2引入了新功能：把edge的标签页也显示在alt tab里。但是恕我直言，这个功能是\*。

关闭方法：

进入设置，搜索Alt+Tab:

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1607951701.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1607951733.png)

以及，还有一个及其恶心的bug：由于我使用了钩子（键位映射，使用mac的键盘布局早已成为习惯），alt+tab时很有可能会改变窗口顺序，比如：我本来在A和B之间切换，结果从A切换到B之后，很短时间内继续切换，则会切换到C窗口而不是A窗口。

也希望在下一个版本中这些问题能够得到解决吧（心累）

