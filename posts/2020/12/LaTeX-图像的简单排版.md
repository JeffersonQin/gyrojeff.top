---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [LaTeX, 技术]
created: '2020-12-17 18:08:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/26.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:56:41'
parent: 0
password: ''
slug: '90'
status: publish
tags: [LaTeX]
template: ''
title: LaTeX | 图像的简单排版
type: post
---
# Preface

写论文时由于有较多图像需要排版，遂学了一把。

# 实现

```latex
\begin{figure}[htb]
	\begin{center}
		\includegraphics[height=5cm]{figures/bad-1.jpg}
		~~
		\fbox{
			\includegraphics[height=5cm]{figures/bad-2.jpg}
		}
	\end{center}
	\caption{图中示例了某款软件只能进行单纯的透视变换，无法处理弯曲边缘}
	\label{fig:bad}
\end{figure}
```

![效果](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608199721.png)

# 用法解释

## figure后的参数[]

- `[h]`  表示的当前位置（here），也就是说图片排在你设置的当前位置，但是如果这一页的空间不足以放下这个图片，此时图片会转到下一页
- `[t]`  顶端 (top)，优先将图片放置在页面的顶部
- `[b]` 底部 (bottom) ，是优先将图片放置在页面的底部
- `[p]`  这个是将图片设置为浮动状态，也就是可以根据系统排版的，自动放置图片的位置

对于组合参数例如`[htb]`：

- 优先使用`h`
- 若无法满足使用`t`
- 最后使用`b`

此外，还可以使用`!`符号来进行强制生效，比如：`[t!]`强制将图片放置在页面上方

## includegraphics

使用`\includegraphics[<options>]{<image path>}`来引入图片：

- `<image path>`是路径
- 常用的`<options>`：
  - `width=<width>`，常用单位：`cm`, `in`, `0.5\textwidth`, `0.5\linewidth`等等
  - `height=<height>`，同上
  - `scale=<scale>`，缩放比例

## caption

`caption`中的内容即为对图片的解释说明

## label

对这张图片设一个标签，方便后续引用，引用时使用`\ref{}`

比如对于前面的`fig:bad`的引用：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608200266.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608200276.png)

> 注：这里有红色框是因为模板的原因，没有的话是正常情况。

# Reference

- [1] https://jingyan.baidu.com/article/c74d6000cc4e1e0f6a595db3.html

