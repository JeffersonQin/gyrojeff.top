---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-07-29 20:56:11'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-29 20:56:11'
parent: 0
password: ''
slug: WSL-2-Ubuntu-2004-调教
status: publish
tags: [Linux, Ubuntu, WSL]
template: ''
title: WSL 2 / Ubuntu 20.04 调教
type: post
---
安装好了`Ubuntu 20.04 / WSL2`, 本文记录一下具体的调教过程。

## 换源

```bash
sudo vim /etc/apt/source.list
```

```
# 添加阿里源
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```

## 安装`proxychains`

```
sudo apt install proxychains4
```

配置`/etc/proxychains.conf`，将最后的`[ProxyList]`改为

```
socks5 <proxy_server_address> <proxy_server_port>
```

注意：由于`WSL 2`是和主机隔离的，所以需要用 **局域网地址**

## 安装`node`以及相关内容

官方参考：

- https://nodejs.org/en/download/package-manager/
- https://github.com/nodesource/distributions/blob/master/README.md

```
# Using Ubuntu
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

注意：第一步可能需要用到加速，配置过`proxychains`的可以将命令改写为：

```
curl -fsSL https://deb.nodesource.com/setup_16.x | proxychains sudo -E bash -
```

安装完毕之后，可以进一步安装`yarn`

```
sudo npm install -g yarn
```

## 配置X11 GUI（详细内容见其他文章）

```bash
export DISPLAY="<windows_ip_address>:0.0"
```

（这里可能会遇到坑，见之前的文章）

## 配置声音输出（详细内容见其他文章）

安装`pulse-audio`

```
sudo apt install pulseaudio
```

配置环境变量: 在`~/.bashrc`末尾加入

```bash
export PULSE_SERVER="tcp:<windows_ip_address>"
```

注意：`<windows_ip_address>`是局域网地址，理由不再赘述。

安装一个`ffmpeg`做一下测试：

```
sudo apt install ffmpeg
```

用`ffplay`做个测试：

```
ffplay <path-to-music>
```

## 安装`x11-apps`

```
sudo apt install x11-apps
```

测试`GUI`

```
xcalc
```

## 安装`VS Code Server`

随便找个目录运行：

```
code .
```

## 安装一系列开发环境/工具

```bash
sudo apt-get install libgtkextra-dev libgconf2-dev libnss3 libasound2 libxtst-dev libgtk-3-0 libnss3-dev libgdk-pixbuf2.0-dev libgtk-3-dev libxss-dev
```

## 安装Chrome试试看

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb
google-chrome
```
