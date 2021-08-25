---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-21 20:05:25'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210825154133.png',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-21 20:05:25'
parent: 0
password: ''
slug: 安卓11明日方舟未成年人不Root破解方案
status: publish
tags: [Android]
template: ''
title: 安卓11明日方舟未成年人不Root破解方案
type: post
---
# Preface

正如前几篇文章所说，我是未成年人，但是又想打明日方舟不受时间限制。今日入了安卓机器，那么自然就像在手机上也这么干。然而，不想解`bootloader`不想`root`就像跑，这注定是一件困难的事，于是乎便有了这篇文章。

# 小黄鸟 (HttpCanary)，启动！

第一步总归是尝试用模拟器上同样的方法复刻咯，这里使用的是小黄鸟 2.x 版本。注意，请不要用 3.x 版本，因为我们无法导出证书。启动小黄鸟 2.x 版本后，显而易见，我们现在不能对方舟做任何事情，并且还会提示网络配置出错，这是因为还没有成功安装 CA 证书。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/ab968cff897592ac8bb8b9af00bfc4f.jpg)

# 尝试在 MIUI 中安装 CA Certificate

在 MIUI 12.5 当中，安装证书的位置隐藏地更深了。

```
安全 => 更多安全设置 => 加密与凭据 => 安装证书 => CA 证书
```

然后选择从 HttpCanary 当中导出的证书进行安装。

我们会发现，HttpCanary 可以导出两种证书：`.pem` 和 `.0`。其称区别为系统范围内和用户范围内的不同证书。然而打开文件可以发现，其内容完全相同。

安装完 CA 证书，我们遗憾地发现，还是行不通。思考后发现这其实只是用户级别安装了证书，但是并没有得到信任，这就是为什么安装了这个证书之后开启小黄鸟抓包浏览器会提示 https 不安全的原因。看来还需要另辟蹊径。

此时我们便想到了 `VirtualXposed` 和 太极

# 关于 `Xposed`

其实就是 `root` 后的一个框架，类似于 `iOS` `jailbreak` 后的 `theos` 框架。可以用 `hook` 等工具拦截函数通讯，于是乎可以做各种黑魔法。

# 关于 `VirtualXposed` 和 太极

而 `VirtualXposed` (`vtxp`) 字面意思则就是 *虚拟的 `Xposed`*。对于 `vtxp` 和 太极，我们可以理解为：

- “伪”沙盒（不能 `hook` 之类的）
- 沙盒内部可以做到一定程度的提权

# 尝试使用 `vtxp` 和 太极

先说结论：失败

原因无非一下几点：

- `VPN` 功能貌似被阉割
- `CA` 证书功能被阉割
- 没有 `root` 权限
- 不支持32位应用（明日方舟）

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/eab955bd9477e5a019275f82a8505c7.jpg)

# `VMOS` (Pro)

最终，决定采用 `VMOS`。思路如下：

```
┌——————————————————————————————————————————
| 主机环境
| * HttpCanary 抓包
├——————————————————————————————————————————
| 虚拟机环境
| * Root Certificate Manager 安装导入的证书
| * 运行软件
└——————————————————————————————————————————
```

其实就是相当于用虚拟机作为跳板，进行静态注入。

最终成功在半夜启动 `Arknights`

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210825154133.png)

# Note

本文不对如何静态注入破解未成年限制做说明，请见其他文章。
