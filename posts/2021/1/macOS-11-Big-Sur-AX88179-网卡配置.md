---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-13 13:37:10'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/12.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-13 13:37:10'
parent: 0
password: ''
slug: '228'
status: publish
tags: []
template: ''
title: macOS 11 Big Sur | AX88179 网卡配置
type: post
---
# Preface

自从我的Mac升级了新系统，恶心的事情基本就没停过，而我最核心的需求，USB网卡的驱动挂了（裂。

于是乎通过万能的搜索引擎找到了一些解决方法，最终采用的是官网的方法。由于在墙外，所以这里给出翻译以及一些tips。

官网：https://www.asix.com.tw/en/product/USBEthernet/Super-Speed_USB_Ethernet/AX88179

# 解决方法

1. 关闭SIP，具体方法可以参考这篇文章：https://sspai.com/post/55066。简而言之就是开机时按住Command ⌘ + R，然后进Terminal输入`csrutil disable`
   
   > SIP 全称为「System Integrity Protection」即「[系统完整性保护](https://support.apple.com/zh-cn/HT204899)」，是 OS X El Capitan 时开始采用的一项安全技术，SIP 将一些文件目录和系统应用保护了起来。但这会影响我们一些使用或设置，比如：更改系统应用图标、终端操作系统目录文件提示「Operation not permitted」、Finder 无法编辑系统目录里的文件。
2. 打开系统扩展开发者模式：进入terminal输入：`systemextensionsctl developer on`
3. 安装beta版驱动，下载：
   ![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610516012.png)

在上述准备工作都做好之后，到launchpad中打开ASIX_USB_Device_App，然后按照上面的步骤进行。

