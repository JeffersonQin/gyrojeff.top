---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [LaTeX, 技术]
created: '2020-12-19 15:38:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/32.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:56:49'
parent: 0
password: ''
slug: '99'
status: publish
tags: [LaTeX]
template: ''
title: LaTeX调整itemize和enumerate间距
type: post
---
# Preface

使用`cvpr`的$\LaTeX$模板的时候发现它的`itemize`距离非常大，故我需要手动调整。写下此文以记录。

# 方法

首先，若需要调整`enumitem`的内容需要引入`enumitem`宏包：

```latex
\usepackage{enumitem}
```

> 注：该宏包的文档, https://mirrors.concertpass.com/tex-archive/macros/latex/contrib/enumitem/enumitem.pdf

下图是一张浅显易懂的位置参考：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608362492.png)

# 简单易懂的实现

```latex
\begin{itemize}[itemsep=2pt,topsep=0pt,parsep=0pt]
	\item item1
	\item item2
	\item item3
\end{itemize}
```

# Reference

- [1] https://mirrors.concertpass.com/tex-archive/macros/latex/contrib/enumitem/enumitem.pdf
- [2] https://blog.csdn.net/robert_chen1988/article/details/83179571

