---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [记录]
created: '2020-12-25 11:10:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/9.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:56:57'
parent: 0
password: ''
slug: '109'
status: publish
tags: [SVP, 补帧]
template: ''
title: SVP补帧 | Potplayer和dandanplay
type: post
---
# Preface

之前的RX560由于种种原因只能吃灰，而Bootcamp后的mbp虽然能够补帧，但是如果想要在我的主显示器上补帧还要切换视频信号，极为繁琐，不易执行。为此，继续研究，现在使用了SVP补帧方案。

# SVP安装

SVP (SmoothVideo Project) 可以前往官网下载：[下载 – SVP – SmoothVideo Project (svp-team.com)](https://www.svp-team.com/zh/get/)

> 这个软件也是我唯一一次的“支持正版”（毕竟太强了orz

在下载完毕之后，我们不妨进入安装界面。由于我还需要给dandanplay补帧，还得安装32位的各种包（骂骂咧咧）

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608865333.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608865385.png)

选择安装所有组件比较保险，然后一路确定下去安装就行。

安装完毕之后，打开SVP时记得选择开启GPU加速，之后会进行压力测试。

# 调教Potplayer

以下图片转自：[PotPlayer+SVP4视频补帧简易教程_GJG666的博客-CSDN博客](https://blog.csdn.net/GJG666/article/details/82949348)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608865919.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608865922.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608865925.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608865927.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608865929.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608865931.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608866134.png)

我：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608887693.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608866173.png)

全部设置完成之后，可以按`tab`来查看帧率。（注：svp后台需要开着），若能够补帧，则可以看到图标出现：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608866268.png)

# 调教dandanplay

建议使用UWP的dandanplay（我也不知道为什么，可能是玄学问题）

设置：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608866380.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608866457.png)

在此之后，在 K-Lite 官方网站下载 K-Lite Mega版：https://codecguide.com/download_k-lite_codec_pack_mega.htm

一路安装就行。

然后如果在dandanplay播放时弹出选相框询问是否需要开启Reclock的话，我选的是永久不要。

# 调教SVP

很讽刺的一点是，我发现好像SVP对影视的优化对于动画更好，因为动画的选项可能出现画面的扭曲感。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608866693.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608866722.png)

上图是我的设置。

---

补：

经过大量阅片发现，“影视”选项对大部分场景，特别是人物移动等优化得较好，而“动画”则会在大块的高速移动部分优化得较好。

例如电车行驶的场景，“影视”选项会出现车窗和景色的扭曲，而“动画”则不会；人物行走时，“动画”选项则会出现部分扭曲。

# Reference

- [1] [PotPlayer+SVP4视频补帧简易教程_GJG666的博客-CSDN博客](https://blog.csdn.net/GJG666/article/details/82949348)
- [2] [下载 – SVP – SmoothVideo Project (svp-team.com)](https://www.svp-team.com/zh/get/)
- [3] [弹弹play (qq.com)](https://support.qq.com/products/104929/blog/5960)
- [4] [Download K-Lite Codec Pack Mega (codecguide.com)](https://codecguide.com/download_k-lite_codec_pack_mega.htm)

