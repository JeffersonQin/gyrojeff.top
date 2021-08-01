---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2020-09-10 13:08:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/ca4c8913c473884c7394b81b6ee8c1c.jpg',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2020-09-10 13:08:00'
parent: 0
password: ''
slug: WPF实现Win32API的调用
status: publish
tags: [.NET, C#, WPF]
template: ''
title: WPF实现Win32API的调用
type: post
---
之前在暑假的时候学了FDU的SoC课程，故对编译时的情况有了一定的了解。在WPF当中，如果我们要调用Win32API（这里以`keybd_event`为例）：

```C#
[DllImport("user32.dll", EntryPoint = "keybd_event", SetLastError = true)]
public static extern void keybd_event(byte bVk, byte bScan, uint dwFlags, uint dwExtraInfo);
```

上述代码的意思也显而易见，`DllImport`是一个`attribute`标签。值得注意的是下面的`extern`关键词。众所周知，`.dll`的全称是`Dynamic Link Library`，即在编译时编译器会根据代码中的声明对库中的方法动态链接。这里的`extern`即，是对`keybd_event`做了声明，并明确是在`user32.dll`当中。其他`Win32API`的调用同理。
