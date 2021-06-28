---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-06-28 22:13:31'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-06-28 22:13:31'
parent: 0
password: ''
slug: 测试工程师雾-爬取番组计划时遇到的Bug
status: publish
tags: [Bug]
template: ''
title: '[测试工程师（雾] 爬取番组计划时遇到的Bug'
type: post
---
在写[project-nichijou/bangumi-spider](https://github.com/project-nichijou/bangumi-spider)的蜘蛛，在爬动画id列表的时候，理论上应该有760(页)*24(行)+11 = 18251条数据

然而无论跑多少次，都只有18247条数据。花了将近三个小时排查完代码问题后，怀疑是数据源的问题。特意打印出了日志，然后发现，好家伙，数据源有问题，重复了。

以下为重复subject id: 110922, 161939, 258944, 274

下面是重复页面：
110922 サイボーグ009 人造人009 剧场版:
https://bgm.tv/anime/browser/?sort=title&page=342
https://bgm.tv/anime/browser/?sort=title&page=343

161939 ぼのぼの 暖暖日记:
https://bgm.tv/anime/browser/?sort=title&page=284
https://bgm.tv/anime/browser/?sort=title&page=285

258944 一寸法師 一寸法师:
https://bgm.tv/anime/browser/?sort=title&page=444
https://bgm.tv/anime/browser/?sort=title&page=445

274 Kanon:
https://bgm.tv/anime/browser/?sort=title&page=88
https://bgm.tv/anime/browser/?sort=title&page=89

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210628225204.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210628225230.png)
