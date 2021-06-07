---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-06-06 13:31:03'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/bd1860aabd54ce3e3a676bd1e615ad3f19329a48.jpg',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-06-06 13:31:03'
parent: 0
password: ''
slug: '292'
status: publish
tags: [Apple, FreeBSD, checkra1n, iOS, 越狱]
template: ''
title: iPad mini 2越狱
type: post
---
# 引言

考完AP Chem，内心空虚，不想学习，遂玩。

# 设备环境

- iPad mini 2
- iOS 12.4.5

# unc0ver - 失败

先尝试了[unc0ver](https://unc0ver.dev/)，但是失败。过程：
1. 下载ipa
2. Xcode新建一个项目，获得一个能用的identifier
3. iOS App Signer给ipa用前面的identifier和证书签名
4. Xcode: Window -> Device & Simulators -> “+”号 -> 安装ipa到设备

注意事项：
1. 检查Xcode Target的最低版本, 不要低于你的设备的版本
2. 如果不能install, 尝试先安装一下空项目
3. 如果代码出现Scene的报错，根据Xcode提示加上`@available`
4. 设备Trust, 不要锁屏

以上就是unc0ver的失败教程，失败原因：安装好unc0ver后第二部exploit kernal重启，反复失败，放弃本方法

# checkra1n

成功方法：[checkra1n](https://checkra.in/)

过程：
1. 在mac上安装应用
2. 根据提示，先进入Recovery，在进入DFU
3. 在重启完成之后，等待片刻，checkra1n会出现在屏幕上
4. 进入checkra1n安装Cydia

注意事项：
1. mac需要允许所有App，具体方法先尝试进入System Preference的安全性里面，再不行百度有一条命令。
2. 耐心

# ssh

进入Cydia，在首页就能看到显眼的`OpenSSH`，根据里面的提示安装. 由于我想把这台老年iPad当作服务器之类的跑跑一些无关紧要的任务，所以IP固定是最好的了，遂在路由器设置中给这个设备做了一下DHCP静态IP分配。

随便选一种方法ssh一下设备`ssh root@<ip>`，初始密码：alpine。我当时连续打错了5次（裂，打成了alphine）

关于SSH修改密码，Cydia当中也给出教程了，其实就是`passwd`命令。

# sftp

既然都能`ssh`了，那我猜测可以使用`sftp`，使用`FileZilla`测了一下，应该是可以的。

# apt

直接输入`apt`，就能看到：超级牛力`apt`!

```
This apt has Super Cow Power
```

# python

让我非常惊讶的是`python2.7`是系统自带的（好像也没啥值得惊讶的，看看一些`linux`发行版就知道了）

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210507170833.png)

# python3

```bash
apt install python3
```

# pip3

```bash
python3 -m ensurepip
```

# vim

既然是BSD系统，怎么能没有`vim`! （雾） 用超级牛力`apt`查了一下，果然还是有的

```bash
apt install vim
```

# curl

```bash
apt install curl
```

# shadowsocks

https://github.com/Qusic/shadowsocks-libev-ios

我首先`apt search`了一下，然后发现有好心人直接一直好了

```bash
apt install me.qusic.shadowsocks
```

配置方法见其他文章。

这个是`libev`的版本，所以启动一个永久服务器命令改成：

```bash
nohup ss-local -c <config_file> start &
```

# proxychains

和`shadowsocks`移植者是同一个人，真不戳。老地方配置，`/etc/proxychains.conf`。方法见其他文件。

测试：

```bash
wget https://www.google.com/
```

# node

```bash
apt install nodejs
```

# ffmpeg

```bash
apt install ffmpeg
```

# clang

本来试了一下`apt install gcc`但是一直报错，后来发现变成了`clang-10`就行

# c++

```bash
clang-cpp-10
```

---

# 整活开始分界线

> 你下一台电脑，何必是电脑

说实话，朋友们，我人傻了。我真的没想到iPad上可以这么整活。

# 弹弹Play动漫花园服务器

https://github.com/JeffersonQin/dandanplay_toolchain

前面配置好了`proxychains`和`shadowsocks`，接下来我们只需要安装一些包就行了

```bash
proxychains pip3 install uvicorn
proxychains pip3 install typing
proxychains pip3 install fastapi
proxychains pip3 install requests
proxychains pip3 install bs4
proxychains pip3 install arrow
```

为了方便，我们把`run_host`改为`0.0.0.0`

```bash
nohup proxychains4 python3 ddp_api_server.py &
```

然后，然后就跑起来了。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/4dc01ba582bd13b2148278f5e43b1a2.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/745b518c9c3da3bc4e35eda5db3fd24.png)

# hexo服务器

`npm`拉一下包

```bash
proxychains npm install -g hexo
```

初始化一下

```bash
proxychains hexo init
```

发现没装`git`

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210507191824.png)

```bash
apt install git
```

再重新执行`hexo init`，然后，就好了。跑一下server:

```bash
hexo server
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/ffa583feaa0000c2f41d739838b600e.png)

# Reference

- https://sevencho.github.io/archives/e38331a.html