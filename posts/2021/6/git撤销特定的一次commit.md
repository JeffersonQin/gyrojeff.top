---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-06-27 19:36:55'
modified: '2021-06-27 19:36:55'
parent: 0
password: ''
slug: git撤销特定的一次commit
status: publish
tags: []
template: ''
title: git撤销特定的一次commit
type: post
---
原文: https://stackoverflow.com/questions/2938301/remove-specific-commit

方法:

```bash
git rebase -i HEAD~x
```

其中`x`为要往前看的`commit`次数。

会有类似于这样的界面：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210627193936.png)

在需要撤销的`commit`前面把`pick`改为`drop`，然后保存即可。

最后，如果这个`commit`已经在`remote`仓库了，需要:

```bash
git push --force-with-lease
```

因为直接`git push --force`是比较严重的。
