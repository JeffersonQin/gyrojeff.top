---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-04-10 02:02:00'
modified: '2021-04-10 02:09:10'
parent: 0
password: ''
slug: Windows下的macOS键盘布局
status: publish
tags: []
template: ''
title: Windows下的macOS键盘布局
type: post
---
由于之前用了五年macOS，后来改到Windows之后实在是习惯不了快捷键的指法，遂使用MapKeyboard 2.1来更改键盘布局。

- LCtrl -> LWin
- LWin -> LAlt
- LAlt -> LCtrl
- RCtrl -> RWin
- RWin -> RAlt
- RAlt -> RCtrl

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210410020428.png)

值得注意的点：
- 有的程序在模拟按键的时候还是会按照原生按键来，比如：Logitech G Hub，远程桌面UWP版
- 不建议使用PowerToy，因为那只是一个钩子程序，很多情况下会出问题。这个应该是通过改注册表实现的。
- 大部分程序都认修改后的配置的，包括著名的AutoHotKey
- 猜测：产生这个问题的原因可能是Win32有两个API，一个读raw一个读触发

同时，这里也使用了AutoHotKey来让Ctrl + Q实现推出程序。脚本：
```
^Q::Send, !{F4}
```
其实就是发送Alt+F4