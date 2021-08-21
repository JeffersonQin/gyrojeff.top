---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-06 00:14:53'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-06 00:14:53'
parent: 0
password: ''
slug: Bluestacks-开启-HttpCanary-卡顿-替代方案
status: publish
tags: [Bluestacks, 抓包]
template: ''
title: Bluestacks 开启 HttpCanary 卡顿 替代方案
type: post
---
# 确定方案

看之前的文章可知，由于一些不可名状的原因，我现在在使用`Hyper-V`这个邪教。随之而来的，便是`BlueStacks for Hyper-V`，相比`Mumu`，这又是一个邪教。所以如果可以，大家还是别碰`Hyper-V`这趟浑水（暴论）。所以，到底是怎么回事呢。由于我未成年，而我又想打方舟，而我手机又用的是iOS但却不想在iOS上玩，而且我还用的是`Hyper-V`。光听这么多前缀就能大概预料到事情有多么烦人了。首先，我们来梳理一下：

(1) 想要打明日方舟<br>
(2) 想要在安卓服上打<br>
(3) 没有安卓设备

 => 使用安卓模拟器<br>
(4) 使用了`Hyper-V`<br>
(5) 大部分主流安卓模拟器使用 `VirtualBox` 作为基础魔改，不支持 `Hyper-V`

 => 只能用 `VMWare Android x86` / `BlueStacks for Hyper-V` / 星云引擎<br>
(6) `VMWare Android x86`十分落后：速度缓慢，不支持虚拟机应有的各种加速<br>
(7) 星云引擎不支持`ADB`<br>
(8) 我要使用游戏外挂，而游戏外挂需要`ADB`注入<br>

 => 只能用 `BlueStacks for Hyper-V`<br>
(9) 我是未成年人，但又想要破解限制

 => 需要使用 `HttpCanary` 等工具抓包静态注入 / 建立代理服务器过滤请求<br>
(10) `BlueStacks for Hyper-V` 对于多任务的优化简直就是灾难。在后台开启 `HttpCanary` 时操作方舟十分卡顿

 => 最终结论：使用`BlueStacks for Hyper-V`, `root`, 使用 `Root Certificate Manager` 安装代理服务器抓 SSL 使用的 CA 证书, 建立代理服务器, 过滤请求, 开始玩.

综上所述，我现在使用这个`repo`:
- https://github.com/Tao0Lu/Arknights_Anti-addiction_Cheater/

如果失效，我还`fork`了一份:
- https://github.com/JeffersonQin/Arknights_Anti-addiction_Cheater/

# 实行

1. `clone`项目下来，安装好应有的依赖。
2. 由于我是在`wsl`环境内运行的，所以我还专门写了一个启动脚本
   ```bat
   wsl -e python3 /mnt/d/ToolChain/Arknights_Anti-addiction_Cheater/fcm.py
   pause
   ```
3. 配置`BlueStacks for Hyper-V`的`proxy server`：
   - 进入`BlueStacks`目录: `C:\Program Files\BlueStacks_bgp64_hyperv`
   - 找到`HD-ConfigHttpProxy.exe`
   - 设置命令：`HD-ConfigHttpProxy.exe set <address> <port>`

大功告成。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210820111839.png)
