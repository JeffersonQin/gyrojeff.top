---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-13 13:52:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/14.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-13 18:07:17'
parent: 0
password: ''
slug: '240'
status: publish
tags: [SMB, VNC, 网络]
template: ''
title: 文件共享 + VNC | Mac和Windows内网组网
type: post
---
# Preface

这个目标其实我早就实现了，这段时间刚修好了macOS 11的有线网卡，于是就再整一次活。

# 配置有线网

Mac：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516339.png)

这里的IP地址以及子网掩码请大家自行思考。我这里是以Mac为Server，Windows为Client做的。理论上只要在一个子网内都没问题。

Windows：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516559.png)

Windows的配置同理

# 配置VNC

Mac端安装VNC Server，长这个样子：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516611.png)

接下来请大家自行百度。

配置：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516774.png)

这里我只配了一下端口

Windows连接：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516824.png)

输入IP地址+冒号+端口号即可，可以看到，支持数个IP地址，不用担心。

# 配置文件共享

先配置成差不多这个样子

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516895.png)

然后点击“选项...”：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516929.png)

Windows上连接：

Win+R呼出运行窗口，输入

```
\\<IP地址>
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516977.png)

会要求输入用户名密码，就是Mac的

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610517002.png)

映射成网络驱动器：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610517034.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610517077.png)

恭喜获得大号U盘一枚（！（逃

