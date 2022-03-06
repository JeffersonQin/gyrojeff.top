---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-06 00:18:49'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-06 00:18:49'
parent: 0
password: ''
slug: ms-word-file-recovery
status: publish
tags: [Microsoft, Word]
template: ''
title: 深藏功与名——MS Word 文件恢复
type: post
---
## 引言

昨晚帮以前的班主任恢复了写了一天的论文。已经不是第一次被拜托类似的需求了，遂特此记录以免日后忘记。

## 方法

具体流程参考[这篇文章](https://www.jianshu.com/p/21e5188ec260)

## 几个问题

(1) 形如：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220306231335.png)

修改后缀名为 `.doc` 或 `.docx` 分别进行尝试。

(2) 形如：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220306231424.png)

将文件放入：

* Windows: `~\AppData\Local\Microsoft\Office\UnsavedFiles`
* MacOS: `~/Library/Containers/com.microsoft.Word/Data/Library/Preferences/AutoRecovery`

注意：这一步骤和电脑无关。我在自己电脑上恢复除了其他电脑上的文件。
