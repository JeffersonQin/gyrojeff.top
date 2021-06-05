---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [LaTeX, 技术]
created: '2020-12-16 23:21:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/58.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:56:38'
parent: 0
password: ''
slug: '89'
status: publish
tags: [CVPR, LaTeX, 模板, 论文]
template: ''
title: LaTeX模板——CVPR | 以及Tikz宏包使用的修复
type: post
---
# Preface

由于要改冬令营的论文，想着$\LaTeX$这回得好好做下排版了，于是便开始搜索模板。

# CVPR模板

在查找模板的过程当中，几经波折。由于我是用的是WSL[Ubuntu]，之前太懒没有装Windows下的TeXLive而且也懒得再倒腾，WSL中的TeX中文环境也只适配了XeTeX，所以这些都成为了找米板路上的阻碍。

最后，在Overleaf上找到了2018年的CVPR模板，如下：https://www.overleaf.com/latex/templates/cvpr-2018-template/qgmrfntfbqns

要使用的话直接复制所有的文件自己创建然后编译就行。

# 关于此模板的Bug

今天由于我需要使用`tikz`宏包画流程图，然而一`include`就会编译过不了，于是借助万能的互联网，找到了如下的解决方案：https://tex.stackexchange.com/questions/373873/sharelatex-cvpr-template-not-compiling-tikz

> That template looks pretty horribly broken. The `eso-pic.sty` file isn't actually the real file. It's hacked to `\input{cvpr_eso.sty}` rather than `\RequirePackage{atbeginshi}` at least. But `cvpr_eso.sty` appears to actually be `everyshi`.
> 
> To fix it, delete `cvpr_eso.sty` and `eso-pic.sty`. Now everything should compile with `\usepackage{tikz}`.
> 
> Other bad things about that template:
> 
> * The `epsfig` package should not be used; `graphicx` replaces it.
> * The `times` package should probably not be used. Use `mathptmx` instead.

所以，只需删除`cvpr_eso.sty`和`eso-pic.sty`，然后不建议使用`epsfig`和`times`宏包即可。

# Reference

- [1] https://tex.stackexchange.com/questions/373873/sharelatex-cvpr-template-not-compiling-tikz
- [2] https://www.overleaf.com/latex/templates/cvpr-2018-template/qgmrfntfbqns

