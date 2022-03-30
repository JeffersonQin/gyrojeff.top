---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-30 00:21:00'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbChoice: default, thumbDesc: '', thumbSmall: '',
  thumbStyle: default}
modified: '2021-11-11 18:55:10'
parent: 0
password: ''
slug: Clash-入土为安
status: publish
tags: [Clash, 科学, 网络]
template: ''
title: Clash 入土为安
type: post
---
本文将讲述如何使用Clash工具。

- [一些 `Links`](#一些-links)
- [安装](#安装)
	- [关于彻底卸载](#关于彻底卸载)
		- [删除配置文件](#删除配置文件)
		- [删除`wintun.dll`](#删除wintundll)
		- [删除网卡](#删除网卡)
	- [开始安装](#开始安装)
- [界面简介](#界面简介)
- [获取配置文件](#获取配置文件)
- [为什么需要`MIXIN`](#为什么需要mixin)
- [配置重置连接](#配置重置连接)
- [配置`TUN`](#配置tun)
	- [安装 TUN 驱动](#安装-tun-驱动)
	- [激活服务](#激活服务)
	- [在配置文件中增加配置 (根据官方Wiki)](#在配置文件中增加配置-根据官方wiki)
	- [验证配置](#验证配置)
	- [Update: 新版本配置方法](#update-新版本配置方法)
- [配置By-Pass](#配置by-pass)
- [配置一些常用的`Mixin`](#配置一些常用的mixin)
- [关于配置时的`process name`](#关于配置时的process-name)
- [配置UWP](#配置uwp)
	- [矛盾](#矛盾)
		- [设置为系统代理情况下](#设置为系统代理情况下)
			- [CFW处理方式](#cfw处理方式)
			- [ss-win处理方式](#ss-win处理方式)
	- [为什么CFW下UWP应用无法联网](#为什么cfw下uwp应用无法联网)
	- [解决办法](#解决办法)
		- [如不使用UWP应用](#如不使用uwp应用)
		- [如要使用不需要代理的UWP应用](#如要使用不需要代理的uwp应用)
		- [如要使用需要代理的UWP应用](#如要使用需要代理的uwp应用)
		- [一些特殊的UWP应用](#一些特殊的uwp应用)
- [关于代理服务器](#关于代理服务器)
- [关于更改代理服务器端口和允许局域网连接问题](#关于更改代理服务器端口和允许局域网连接问题)
- [关于 Switchy-Omega (SO) 使用](#关于-switchy-omega-so-使用)
- [关于新版本`pip`报错无法使用问题](#关于新版本pip报错无法使用问题)
- [总结](#总结)

# 一些 `Links`

- Clash repo: https://github.com/Dreamacro/clash
- Clash for Windows 文档: https://docs.cfw.lbyczf.com/
- Clash for Windows repo: https://github.com/Fndroid/clash_for_windows_pkg

# 安装

安装是一个非常繁琐的过程，特别是装错过一次的时候。

## 关于彻底卸载

如果没有自己瞎搞过的朋友可以跳过这一章节。

### 删除配置文件

- `%APPDATA%`
- `~/.config/`

### 删除`wintun.dll`

### 删除网卡

在控制面板中无法删除：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830003208.png)

在设备管理器中卸载。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830004054.png)

## 开始安装

前往项目GitHub的Release Page下载最新的Setup，然后双击安装。

# 界面简介

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830104538.png)

对于`Proxies`和`Profiles`可以这么理解：

* `Profiles`就是飞机场的订阅链接的内容
* `Proxies`中的一系列设置反应`Profiles`中的内容

拿某个机场的配置文件举例：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830105454.png)

* `proxies`字段，列举了所有`node`
* `proxy-group`字段，则是开始定义各种模式
  * `Proxy`模式：可以选择底下列的一堆节点中的某一个节点
  * `MATCH`模式：可以选择`Proxy`模式选择的节点或者`DIRECT` (直连) 模式
  * `Streaming`模式：可以选择`Proxy`的模式或其他一堆节点

# 获取配置文件

略。有订阅链接后：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830112023.png)

# 为什么需要`MIXIN`

由于我们可能需要取消或者增加某些代理选项，所以我们需要修改配置文件。然而，即使我们修改了配置文件，订阅连接刷新时我们的修改就会被覆盖。所以，我们需要在配置文件以外的地方增加配置，由此产生了`Mixin`

注意：配置`Mixin`时的任意字段对于其他的配置文件而言，**不是增加**，而**是覆盖**。

举个例子：

原来的配置文件有`rules`:

```yaml
...
rules:
 - DOMAIN,app.adjust.com,DIRECT
 - DOMAIN,bdtj.tagtic.cn,DIRECT
 - DOMAIN,log.mmstat.com,DIRECT
 - DOMAIN,sycm.mmstat.com,DIRECT
 ...
 ...
```

我们需要增加一条新的规则：

```yaml
- DOMAIN, cdn.jsdelivr.net,DIRECT
```

在`mixin`中，如果只写成下面这样是不行的：

```yaml
mixin:
  rules:
  - DOMAIN, cdn.jsdelivr.net,DIRECT
```

因为这样就把原来复杂的规则组变成了`- DOMAIN, cdn.jsdelivr.net,DIRECT`这样一句话。所以我们还需要复制原来的规则组，在规则组最上方添加我们的规则（优先级高）：

```yaml
mixin:
  rules:
  # custom
  - DOMAIN, cdn.jsdelivr.net,DIRECT
  # default
  - DOMAIN,app.adjust.com,DIRECT
  - DOMAIN,bdtj.tagtic.cn,DIRECT
  - DOMAIN,log.mmstat.com,DIRECT
  - DOMAIN,sycm.mmstat.com,DIRECT
  ...
  ...
```

注意：上面都是用 `yaml` 的方法配置的 `mixin`，还可以用 `Javascript` 的方式配置 `mixin`，方法可以参考官方文档和我的这个项目：https://github.com/JeffersonQin/clash-multi-mixin 其可以让 `clash` 的 `mixin` 同时支持多个飞机场。具体使用方法参见项目的 README。

# 配置重置连接

Settings中设置。此字段的意思为：如果规则改变了，需要重新开始所有连接。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830015209.png)

# 配置`TUN`

`TUN`即隧道，虚拟网卡，接管所有流量，交给`Clash`处理。这个才是选择`Clash`的主要理由。

首先是替换`wintun.dll`。这个步骤可能不是必须的，但在老版本中可能需要，所以还是说一下。

**[2022/02/20] Update: 新版本的配置方法可能会有些许不同，参考本节最后一小节。**

## 安装 TUN 驱动

- https://www.wintun.net/

在上面的页面下载驱动。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830103639.png)

在打开的文件夹中，用下载下来的文件替换掉`wintun.dll`

## 激活服务

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830103729.png)

直到框中的小地球为绿色，尝试`Manage` (`Install` / `Uninstall`) 反复尝试。

## 在配置文件中增加配置 (根据官方Wiki)

前面提到了`Mixin`，这里我们就直接在`mixin`中配置。在`mixin`字段中增加如下字段：

```yaml
dns:
  enable: true
  enhanced-mode: redir-host
  nameserver:
  - 223.5.5.5
  - 223.6.6.6
tun:
  enable: true
  stack: gvisor # 使用 system 需要 Clash Premium 2021.05.08 及更高版本
  dns-hijack:
  - 198.18.0.2:53 # 请勿更改
  auto-route: true
  auto-detect-interface: true # 自动检测出口网卡
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830111853.png)

## 验证配置

到达这一步，虽然没有发挥出全部功力，但是已经能够正常上网了。这里，建议使用`SwitchyOmega`的插件辅助验证。

在浏览器中，`SwitchyOmega`设置为 **直连模式** 。

## Update: 新版本配置方法

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220220224236.png)

点击 TUN 的设置小按钮，进行配置：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220220224324.png)

# 配置By-Pass

配置**Clash 系统代理**需要绕过的东西

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830100421.png)

示例：

```yaml
bypass:
  - localhost
  - 127.*
  - 10.*
  - 172.16.*
  - 172.17.*
  - 172.18.*
  - 172.19.*
  - 172.20.*
  - 172.21.*
  - 172.22.*
  - 172.23.*
  - 172.24.*
  - 172.25.*
  - 172.26.*
  - 172.27.*
  - 172.28.*
  - 172.29.*
  - 172.30.*
  - 172.31.*
  - '*.163.com'
  - '*.126.com'
  - '*.126.net'
  - 'music.163.com' # 网易云域名1
  - '*.music.126.net' # 网易云域名2
  - '*.msftncsi.com'
  - '*.kuwo.cn' # 酷我音乐
  - '*.iqiyi.com' # 爱奇艺
  - 192.168.*
  - <local>
```

# 配置一些常用的`Mixin`

```yaml
rules:
- PROCESS-NAME,qbittorrent.exe,DIRECT
- PROCESS-NAME,NeteaseMusic.exe,DIRECT
- DOMAIN,clash.razord.top,DIRECT
- DOMAIN,yacd.haishan.me,DIRECT
- DOMAIN-SUFFIX,live.com,Proxy
- DOMAIN-SUFFIX,live.net,Proxy
- DOMAIN-SUFFIX,office.com,Proxy
- DOMAIN-SUFFIX,office.net,Proxy
- DOMAIN-SUFFIX,sharepoint.com,Proxy
- DOMAIN-SUFFIX,office365.com,Proxy
- DOMAIN-SUFFIX,officeppe.net,Proxy
- DOMAIN-SUFFIX,skype.com,Proxy
- DOMAIN-SUFFIX,onedrive.com,Proxy
- DOMAIN-SUFFIX,msocsp.com,Proxy
- DOMAIN-SUFFIX,msftncsi.com,DIRECT
- DOMAIN-SUFFIX,msauthimages.net,Proxy
- DOMAIN-SUFFIX,msauth.net,Proxy
- DOMAIN-SUFFIX,microsoftonline.com,Proxy
- DOMAIN-SUFFIX,msn.com,Proxy
- DOMAIN-SUFFIX,onenote.com,Proxy
- DOMAIN-SUFFIX,onenote.net,Proxy
- DOMAIN-SUFFIX,1drv.com,Proxy
- DOMAIN-SUFFIX,cdn.jsdelivr.net,DIRECT
- DOMAIN-SUFFIX,cdn.v2ex.com,Proxy
```

注意：
* 这里的`Proxy`是我自己的规则组名称，记得替换成你们的

# 关于配置时的`process name`

善用工具`tasklist.exe`，里面有`process name`。注意：基本上，大概率，大部分情况，`process name`是有`.exe`的后缀的。之前可靠大先辈发给我的一份当中没有`.exe`，可能是不同版本间的差异，是个坑。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830122739.png)

# 配置UWP

官方Wiki对于UWP不能联网的解释：https://github.com/Fndroid/clash_for_windows_pkg/wiki/UWP%E5%BA%94%E7%94%A8%E8%81%94%E7%BD%91

为了排除隐患，这里特别复制一份引用：

---

## 矛盾

Clash for Windows（下简称CFW）与[shadowsocks-windows](https://github.com/shadowsocks/shadowsocks-windows)（下简称ss-win）最大的区别就是流量处理的方式上。

CFW使用策略组的方式允许用户对请求目的地进行动态调整，而ss-win则只能根据规则判断是直连还是代理。

### 设置为系统代理情况下

#### CFW处理方式

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830103000.png)

#### ss-win处理方式

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830103015.png)

可以看到，最大的区别就是对于直接连接的请求的处理方式，进入ss-win的所有请求均是需要代理的。

## 为什么CFW下UWP应用无法联网

默认的情况下，UWP应用会被禁止使用**回环地址**（127.0.0.1）的代理，而CFW和ss-win均是本地代理，都是通过回环地址提供代理的。

通过[ss-win的处理方式](#ss-win处理方式)可以知道，PAC分流会让系统放行直连流量，所以大部分不需要代理的UWP应用都可以在ss-win的全局PAC分流下使用。而CFW除了内置的几个cfw-bypass字段以外，所有流量都需要处理，这就使得几乎所有的UWP在开启系统代理时都不可用。

## 解决办法

### 如不使用UWP应用

直接开启CFW的系统代理（勾选System Proxy)即可让大部分软件使用代理连接并分流。

### 如要使用不需要代理的UWP应用

不建议开启CFW的系统代理，而推荐给需要代理的非UWP软件单独设置http或socks5代理，不设置系统代理的情况下，不会影响UWP应用联网。浏览器推荐使用[SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega)。

### 如要使用需要代理的UWP应用

大部分UWP应用都没有开放代理设置功能（目前仅发现Telegram有），此时要让UWP使用代理就必须打开系统代理，同时需要在General界面点击**EnableLoopback.exe**并为需要代理的UWP应用解除回环代理限制。

### 一些特殊的UWP应用

网易云音乐：网易云音乐在设置系统代理后直接无法联网，开启Loopback限制也无效，可以使用cfw-bypass字段解除，[阅读文档](https://docs.cfw.lbyczf.com/rao-guo-xi-tong-dai-li)。

Instagram：禁止使用代理，所以无解。

---

至此，是官方对于`UWP`的解释，然而我们仍然可以用其他工具接触UWP的回环地址代理限制。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830103229.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830103322.png)

# 关于代理服务器

默认情况下，`7890`端口是代理服务器的端口。然而，当我尝试在`WSL2`中使用`proxychains`的时候却无论如何无法访问这个代理服务器。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830100046.png)

经查，需要在防火墙上打洞。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830095935.png)

既需要UDP，有需要TCP。配置之后，能够正常使用。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830100112.png)

# 关于更改代理服务器端口和允许局域网连接问题

需要在多个地方都进行更改：
* General 面板
* `Mixin`

示例：

```yaml
mixin: # object
  port: 7890
  socks-port: 7891
  allow-lan: true
```

# 关于 Switchy-Omega (SO) 使用

- 在Clash启用TUN时，SO直连模式可以连接
- 在Clash启用系统代理时，SO系统代理可以连接
- 此外，SO可以连接Clash的Proxy Server

注意：没有开启系统代理时SO仍然可以使用系统代理连接。推测原因：默认不使用代理，走了TUN通道，流量被接管。

# 关于新版本`pip`报错无法使用问题

启用系统代理后就会报这个错，关闭即可。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210830124527.png)

> 经网友提示，pip 报错需要打开clash 配置的Specify Protocol 选项。

# 总结

* System Proxy : 设置系统代理（感觉没啥用）
* 开启 Clash，激活 TUN，虚拟网卡接管流量
* 全部走代理：Global
* 规则：Rule
* 直连：Direct
* 代理服务器只要开了 Clash 就会常开
