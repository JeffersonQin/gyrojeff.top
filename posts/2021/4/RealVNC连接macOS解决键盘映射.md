---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-04-10 02:00:00'
modified: '2021-04-10 02:27:13'
parent: 0
password: ''
slug: RealVNC连接macOS解决键盘映射
status: publish
tags: []
template: ''
title: RealVNC连接macOS解决键盘映射
type: post
---
由于鄙人的键盘布局十分复杂，所以很多工具用起来都十分困难，具体情况见上一篇文章。

在Windows中使用RealVNC连接macOS的时候，我们会发现，快捷键出现了大面积的异常，甚至Alt键就直接消失了。为了解决这个问题，我深入研究VNC Viewer的配置，结果一无所获。就在此时，我意外地发现，remapping的设置是在Server端实现的，这让我格外欣喜。下面是配置：

- LeftCmdKey : Super_L
- LeftOptKey : Super_L
- RightCmdKey : Super_R
- RightOptKey : Super_R

进行到这一步，我们发现，新的映射关系是：

- LWin (Win下的macOS键位) -> LCtrl (macOS接收键位)
- LAlt (Win下的macOS键位) -> LAlt (macOS接收键位)
- LCtrl (Win下的macOS键位) -> LCmd (macOS接收键位)
- RWin (Win下的macOS键位) -> RCtrl (macOS接收键位)
- RAlt (Win下的macOS键位) -> RAlt (macOS接收键位)
- RCtrl (Win下的macOS键位) -> RCmd (macOS接收键位)

我们发现，Alt键回来了，并且已经配好了，但是Win/Cmd和Ctrl互换了。可能会有人问为什么前面要设成Super而不是Ctrl，其实我也想，只不过只能选Super/Alt/ExtendedChar，无解的。

此时，我们发现，好心的RealVNC还提供了RemapKeys选项，看到描述中说，它是根据hexadecimal keysyms标准来的：https://www.tcl.tk/man/tcl8.6/TkCmd/keysyms.htm

那么我们不妨直接交换Super_L, Ctrl_L和Super_R, Ctrl_R, 代码：

```
0xFFE3<>0xFFEB,0xFFE4<>0xFFEC
```

Apply一下，发现完美解决问题。下图是配置：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210410021443.png)

然而，事情到此并没有结束。还记得我上一篇提到的AutoHotKey在Win下Ctrl+Q模拟Alt+F4吗？到了VNC中，Ctrl+Q (Cmd+Q)被AutoHotKey拦截了，无法正常触发。所以我们要解决在VNC中AutoHotKey的触发问题。

查阅API，发现以下方法：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210410022404.png)

这是个很好的解决途径，我们只需要获取VNC窗口的ahk_class或者title之类的就行了。调出AutoHotKey的WinSpy工具：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210410022510.png)

很方便地查出了ahk_class:

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210410022619.png)

最终改了一下代码就好了：

```
#IfWinNotActive ahk_class vwr::CDesktopWin
^Q::Send, !{F4}
```

如果不能复现，建议自己实践。