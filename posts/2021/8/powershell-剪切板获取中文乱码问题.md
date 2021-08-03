---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-04 00:05:13'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-04 00:05:13'
parent: 0
password: ''
slug: powershell-剪切板获取中文乱码问题
status: publish
tags: [.NET, Powershell]
template: ''
title: powershell 剪切板获取中文乱码问题
type: post
---
这几天看到一个很不错的项目：https://github.com/telesoho/vscode-markdown-paste-image/

但是在`Windows`下某些功能出了问题，于是乎我提了个`issue`: https://github.com/telesoho/vscode-markdown-paste-image/issues/44

根据作者的回复，发现`powershell`获取`Html`的剪切板会出问题，貌似是个`bug`。所以我们要解决它（确信）。

> 由于我不用Windows和Mac，目前只完成了Linux中把HTML转换为Markdown的功能。我曾经尝试过在Windows中使用powershell去获取剪贴板中的内容，但是当时windows中有一个bug，如果剪贴板内容中有中文或日文字符时，将剪贴板中的文本转换为UTF编码会出现非法字符，导致无法取出正确的剪贴板内容，所以就放弃了。
>
> 目前如果在windows下想使用HTML转换为Markdown的功能，可以通过直接拷贝HTML的代码，然后用插件粘贴到markdown里，插件会判断粘贴的文本中是否带有html的tag的标记，如果有它就会尝试转换。

# 测试

首先复制一段浏览器中带中文的的富文本，执行命令：

```
Get-Clipboard -TextFormatType Html
```

果然有问题：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804001738.png)

# 尝试解决

第一时间想到了字符集的问题，果断：

```powershell
chcp 65001
```

(上面是将活动页的编码改为UTF-8)

但是无果，还是一样。

# 灵机一动

这两天正好在写WPF，之前也写过`Clipboard`，于是乎搜。问题解决：

```powershell
Add-Type -Assembly PresentationCore
[Windows.Clipboard]::GetData([Windows.DataFormats]::Html)
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804002308.png)

# Reference:

- https://docs.microsoft.com/en-us/dotnet/api/system.windows.dataformats?view=net-5.0
- https://www.cnblogs.com/guogangj/p/7465951.html
- https://blog.vichamp.com/2014/07/21/forward-chinese-text-into-clipboard/
