---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [随想]
created: '2021-08-20 14:41:39'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-20 14:41:39'
parent: 0
password: ''
slug: 吐槽-写代码就是叠BUFF
status: publish
tags: [随想]
template: ''
title: 吐槽 | 写代码就是叠BUFF
type: post
---
我的变态需求真的是越来越离谱了。什么，在`WSL`内获取`Windows`剪切板、`FFI`加速原生调用、未成年+想挂脚本+还想不受限制打游戏。

这也导致我的调试难度和干活难度直线飙升。。。随便举几个最近的离谱例子：

- 在C# 里p/invoke调用cpp写的dll 并调试dll中调用win32api的某个函数
- 调试 在Windows下的启用了WSL远程插件的VSCode中WSL模式的插件 这个插件是用node写的 我要调试的功能调用了linux下的shell的某个脚本 这个脚本调用了某个返回windows主机调用powershell的脚本

总之就是非常离谱。。。

附上一张调试`VSCode PicGo`插件时的截图：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210820144221.png)
