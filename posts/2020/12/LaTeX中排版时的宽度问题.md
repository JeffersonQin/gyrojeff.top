---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [LaTeX, 技术]
created: '2020-12-18 11:41:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/23.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:56:46'
parent: 0
password: ''
slug: '97'
status: publish
tags: [LaTeX]
template: ''
title: LaTeX中排版时的宽度问题
type: post
---
> 注：本文部分转载自[http://liam0205.me/2015/08/17/width-in-latex/](http://liam0205.me/2015/08/17/width-in-latex/)，以及https://blog.csdn.net/robert_chen1988/article/details/52739825

# 宽度

涉及到页面宽度，有这么几个：

* `\linewidth` - 当前行的宽度
* `\columnwidth` - 当前分栏的宽度
* `\textwidth` - 整个页面版芯的宽度
* `\paperwidth` - 整个页面纸张的宽度
* `\hsize` - Plain TeX 的宏，是 TeX 在行末考虑分词换行时使用的宽度

这些宽度里，`\hsize` 是 Plain TeX 的宏，不推荐 LaTeX 用户使用，简单地当它不存在就好了。 在单栏文本中，`\columnwidth` 和 `\textwidth` 保持一致；在多栏文本中 `\textwidth = n * \columnwidth + (n - 1) * \columnsep`（其中 n 是分栏数）。 在 minipage 环境中，除了 `\paperwidth` 之外，其它三个 `\****width` 都会根据 minipage 的宽度发生改变（因为虚拟出了一个小的纸张页面），然后在 minipage 环境结束的时候恢复原样。在 parbox 中，`\textwidth` 和 `\columnwidth` 不会改变，不过 `\linewidth` 会发生变化。 `\linewidth` 是相对最灵活的宽度值。在 list 环境里（包括 enumerate 和 itemize 等环境），在 `\parbox` 里，`\linewidth` 都会发生变化。 总的来说，当* 需要在列表环境中使用表格、图片等宽度的时候，用 `\linewidth`

* 需要充满整个页面宽度的时候，用 `\textwidth` （比如 figure*/table* 等）
* 需要充满整个分栏的时候，用 `\columnwidth` （比如 figure/table/tabularx/tabu 等）

# 单位

| 单位 | 名称 | 说明 |
| - | - | - |
| mm | 毫米 | 1 mm = 2.845 pt |
| pt | 点 | 1 pt = 0.351 mm |
| bp | 大点 | 1 bp = 0.353 mm > 1 pt |
| dd | 迪多 | 1 dd = 0.376 mm = 1.07 pt |
| pc | 排卡 | 1 pc = 4.218 mm = 12 pt |
| sp | 定标点 | 65536 sp = 1 pt |
| cm | 厘米 | 1 cm= 10 mm= 28.453 pt |
| cc | 西塞罗 | 1 cc= 4.513 mm= 12 dd = 12.84 pt |
| in | 英寸 | 1 in = 25.4 mm = 72.27 pt |
| ex | ex | 1 ex = 当前字体尺寸中 x 的高度 |
| em | em | 1 em = 当前字体尺寸中 M 的宽度 |

# 使用感受

相较于固定宽度，个人认为使用比例结合`***width`更为常用，可以较好地进行排版。

