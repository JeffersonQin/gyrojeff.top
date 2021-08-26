---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-25 20:59:25'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-26 14:07:29'
parent: 0
password: ''
slug: VSCode-解决终端渲染模糊问题
status: publish
tags: [VSCode]
template: ''
title: VSCode 解决终端渲染模糊问题
type: post
---
# Preface

之前可靠大先辈说`VSCode`的`terminal`渲染极端模糊，于是弃了`VSCode`。

这是不对的，我必定找出办法把人拉回`VSCode`。

# 搜索关键词：VSCode terminal rendering style

成功查到了方案：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/b955d25cdafe7488511dc465f3dafc4.png)

只需要在配置中加上：

```json
{
	"terminal.integrated.gpuAcceleration": "off"
}
```

就可以关闭 GPU 加速，使用 DOM 渲染，而非 Canvas 渲染。

前：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210826125924.png)

后：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210826130035.png)

# 限制

如果终端中有大量的文件路径，则会非常缓慢。
