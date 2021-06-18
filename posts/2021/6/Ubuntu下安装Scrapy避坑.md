---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-06-18 21:16:30'
modified: '2021-06-18 21:16:30'
parent: 0
password: ''
slug: Ubuntu下安装Scrapy避坑
status: publish
tags: []
template: ''
title: Ubuntu下安装Scrapy避坑
type: post
---
# 引言

因为最近在做的`project nichijou`需要写爬虫，遂准备学习一下`Scrapy`，但是安装中遇到了一些问题。

# 解决

下面是官方的安装文档：

https://docs.scrapy.org/en/latest/intro/install.html#intro-install

你可能同时需要安装一些其他组件，诸如`rust`，但是他们都很好解决，因为我们可以用超级牛力apt来进行安装。

但是安装完成之后，跑官方的demo的时候，却尴尬地发现：

```
jefferson@HQWORKSTATION:~/.local/bin$ scrapy

Command 'scrapy' not found, did you mean:

  command 'scrappy' from deb libscrappy-perl
  command 'scapy' from deb python-scapy

Try: sudo apt install <deb name>
```

这个时候我们不妨运行一下：

```bash
find / -name scrapy
```

来寻找一下文件位置：

```
jefferson@HQWORKSTATION:/usr/local/lib/python3.6$ find / -name scrapy
find: ‘/etc/polkit-1/localauthority’: Permission denied
find: ‘/etc/ssl/private’: Permission denied
/home/jefferson/.local/bin/scrapy
/home/jefferson/.local/lib/python2.7/site-packages/scrapy
/home/jefferson/.local/lib/python3.6/site-packages/scrapy
find: ‘/mnt/c/$Recycle.Bin/S-1-5-18’: Permission denied
find: ‘/mnt/c/$Recycle.Bin/S-1-5-21-4187809054-3396752881-3590665346-1000’: Permission denied
^C
```

找到路径之后，定位过去，看一下版本：

```
jefferson@HQWORKSTATION:~/.local/bin$ ./scrapy version
Scrapy 2.5.0
```

为了以后方便使用做一个软链接 (路径根据自己的来)：

```
sudo ln -s ~/.local/bin/scrapy /usr/bin/scrapy
```

测试

```
scrapy version
```
