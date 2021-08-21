---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-20 14:41:12'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-20 14:41:12'
parent: 0
password: ''
slug: wslpath参数中含有空格的解决方案
status: publish
tags: [Shell, WSL]
template: ''
title: wslpath参数中含有空格的解决方案
type: post
---
用wslpath转换路径, 直接敲命令的话, 如果路径有空格, 就直接转义就行了：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/e29a66578c12d34bd8ff09343f61c0c.png)

但我现在写成了脚本就会报错：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/da11c9cd0bf5a83d10ef6514bf3a108.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/34dfd65f6db74eba3d918287f40e130.png)

加上双引号也没用：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/6685eedd4dea668e52c478d10d0524c.png)

最后感谢ydw找到了`cygwin`相同问题的解决方案：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/8b2156dfcf332fb438fbb5650fa6f6b.png)

最后的解决方案：

```
p="xxx/xxx/xxx/xxx"
wslpath -u "${p}"
```
