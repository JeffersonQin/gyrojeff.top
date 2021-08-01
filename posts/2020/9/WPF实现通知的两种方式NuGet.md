---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2020-09-10 13:08:43'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2020-09-10 13:08:43'
parent: 0
password: ''
slug: WPF实现通知的两种方式NuGet
status: publish
tags: [.NET, C#, WPF]
template: ''
title: WPF实现通知的两种方式NuGet
type: post
---

# `Hardcodet.NotifyIcon.Wpf`

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801105550.png)

这个是使用系统托盘图标来完成通知的推送的，前半段请参照我的其他文章(WPF实现系统托盘)

推送通知的例子：
```C#
App.TaskbarIcon.ShowBalloonTip("UIToy Notification", "Window Rect is null.", Hardcodet.Wpf.TaskbarNotification.BalloonIcon.Error);
```

这里我直接把`TaskbarIcon`当作全局的静态变量定义在`App.xaml.cs`里面了。

# `Notifications.Wpf`

具体用法可以参见作者GitHub：https://github.com/Federerer/Notifications.Wpf

例子：
```C#
MaskWindowManager.notificationManager.Show(
    new NotificationContent 
    { 
        Title = "UIToy Notification", 
        Message = "UI Analyzation Started.", 
        Type = NotificationType.Information 
    }, areaName: "WindowArea");
```
