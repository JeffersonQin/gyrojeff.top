---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [建站, 技术]
created: '2020-11-15 22:10:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/56fa3d154b48510e3969c639ce6998e.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:17'
parent: 0
password: ''
slug: '14'
status: publish
tags: [CSS, HTML, JS, PHP, Typecho, handsome, 神代綺凜, 美化, 魔改]
template: ''
title: handsome神代綺凜式魔改主题插件再魔改
type: post
---
# Preface

由于看了**神代綺凜**巨佬的博客，遂有了改造之意，后来又看到了**Sanakey**大佬的插件封装，遂在其之上继续改进。

（鬼知道我在造轮子的过程中发现已经有了轮子的时候的心情（悲

下面的网站连接就是原址（（

[post url="http://moe.best" title="# 神代綺凜の随波逐流" intro="MOE IS JUSTICE ✟ TECHNOLOGY IS POWER<br>萌は正義なのです ✟ テクノロジーがあれば何でもできるです" cover="https://moe.best/usr/img/avatar1.png"/]

[post url="https://keymoe.com/archives/31/" title="handsome神代綺凜式魔改主题插件" cover="https://keymoe.com/asset/imgs/shinku.gif"/]

# 实现的功能

## 时光机部分优化

- 居中
- 限制最大宽度
- 根据亮暗主题分别做背景调整

## 侧边栏部分优化

- 侧边栏透明度调整
- 侧边栏悬浮透明度调整

## 标签云优化

- 修改标签云字体为白色

## 其他

- 右下角footer版权修改
- console输出留名

## 实现方法

在`kirin.css`中：

```css
/*

* Date: 2020/11/15
* Modified by: JeffersonQin
* Website: https://gyrojeff.top/
* 
* 一开始看到的是JindaiKirin大佬的魔改，后来看到Sanakey大佬的封装Plugin
* 但是仔细调试后仍发现了一下问题，特此修改。
* 既然看到这里来，希望您能在footer里加上：
* Theme modified by <a href="https://gyrojeff.top" target="_blank">JeffersonQin</a>
  */

/* Comments */

.bg-white {
max-width: 1024px;
margin: auto;
}

.comment-respond .panel-default {
max-width: 1024px;
margin: auto;
}

aside.col.w-md.no-border-xs {
background: rgba(255, 255, 255, 0.9)
}

aside.col.w-md.no-border-xs:hover {
background: rgba(255, 255, 255, 0.98)
}

html.theme-dark .col.asideBar.w-md:hover {
background: rgba(0, 0, 0, 0.98)
}

html.theme-dark .post-tags a {
color: #fff
}

html.theme-dark .badge {
color: #fff
}

/* Modification by JeffersonQin ended*/
```

在`kirin.js`中：

```javascript
console.log("\n %c handsome modified %c by JeffersonQin | gyrojeff.top \n",
            "color:#444;background:#eee;padding:5px 0;", "color:#fff;background:#7266ba;padding:5px 0;");
```

# 项目地址以及使用方法

本项目为Sanakey大佬原项目的fork版本，地址：

[button color="dark" icon="github" url="https:\/\/github.com/JeffersonQin/KirinShiKi"]JeffersonQin/KirinShiKi[/button]

# 尾声

欢迎email、wechat、qq、或者提交PR！

# Reference

- [1] moe.best
- [2] https://keymoe.com/archives/10/
- [3] https://keymoe.com/archives/31/

