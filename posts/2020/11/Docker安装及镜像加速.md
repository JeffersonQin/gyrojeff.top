---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2020-11-18 10:25:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/40.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:31'
parent: 0
password: ''
slug: '38'
status: publish
tags: [Docker, Linux, 阿里巴巴]
template: ''
title: Docker安装及镜像加速
type: post
---
# 安装Docker

## 卸载旧版本

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

## 设置仓库

安装apt依赖包：

```bash
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

添加官方GPG密钥：

```bash
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88 通过搜索指纹的后8个字符，验证您现在是否拥有带有指纹的密钥。

```bash
$ sudo apt-key fingerprint 0EBFCD88
   
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

使用以下指令设置稳定版仓库

```bash
sudo add-apt-repository \
   "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/ \
  $(lsb_release -cs) \
  stable"
```

## 安装 Docker Engine-Community

更新 apt 包索引。

```bash
sudo apt-get update
```

安装最新版本的 Docker Engine-Community 和 containerd:

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

# Docker换源

1. 先进入阿里云开发社区,并找到控制台：https://developer.aliyun.com/
2. 搜索容器镜像服务：
   ![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605666829.jpg)
3. 按照图片当中的做：
   ![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605666853.jpg)

确认成功的方法：

```bash
sudo docker info
```

观察最后几行当中Registry Mirrors有没有出现。

