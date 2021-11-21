---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-10-04 11:33:47'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-10-04 11:33:47'
parent: 0
password: ''
slug: support-mouse-wheel-in-epub-reader-of-calibre-web
status: publish
tags: [Calibre, Javascript, Web]
template: ''
title: 让 Calibre-Web 的 EPUB 阅读器支持滚轮滚动
type: post
---
# Introduction

Github repo: https://github.com/janeczku

这个 Calibre-Web 是一个非常好用的能够将 Calibre 的数据库做成 Server 浏览的一个项目。

但是，`epub-js` 的版本老旧且功能不够，特别是电脑上不能用滚轮翻页，这对我这种~~我重度残废~~很不友好，所以就像魔改一下功能。

# 原理介绍

旨在对下图黄色区域增加滚轮支持。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211121104748.png)

至于为什么蓝色区域不行，见下图：蓝色区域是 `iframe`，懒得折腾了。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211121104858.png)

# 逆向后的代码增删

首先逆 `minify`，下面是增添内容：

```diff
...
p = function (b) {
	window.alert("here!");
	37 == b.keyCode && ("rtl" === a.package.metadata.direction ? h.next(): h.prev(),
		f.addClass("active"), (o = !0),
		setTimeout(function () {
			(o = !1), f.removeClass("active");
		}, 100),
		b.preventDefault()),
	39 == b.keyCode && ("rtl" === a.package.metadata.direction ? h.prev() : h.next(),
		e.addClass("active"), (o = !0),
		setTimeout(function () {
			(o = !1), e.removeClass("active");
		}, 100),
		b.preventDefault());
- };
+ },
+ eventHandle = {
+ 	getEvent: function(event){
+ 		return event || window.event;
+ 	},
+ 	addEvent: function(element, type, handler){
+ 		if(element.addEventListener){
+ 			element.addEventListener(type, handler, false);
+ 		}
+ 		else if(element.attachEvent){
+ 			element.attachEvent('on'+type, handler);
+ 		}else{
+ 			element['on'+type] = handler;
+ 		}
+ 	},
+ 	getWheelDelta: function(event){
+ 		return event.wheelDelta ? event.wheelDelta : (-event.detail) * 40;
+ 	}
+ },
+ mouseHandle = function(event) {
+ 	event = eventHandle.getEvent(event);
+ 	var delta = eventHandle.getWheelDelta(event);
+ 	if (delta < 0) "rtl" === a.package.metadata.direction ? h.prev() : h.next();
+ 	else "rtl" === a.package.metadata.direction ? h.next() : h.prev();
+ };
return (
	document.addEventListener("keydown", p, !1),
+ 	eventHandle.addEvent(document, 'mousewheel', mouseHandle),
+ 	eventHandle.addEvent(document, 'DOMMouseScroll', mouseHandle),
...

```

Minify 后：

```javascript
eventHandle={getEvent:function(event){return event||window.event},addEvent:function(element,type,handler){if(element.addEventListener){element.addEventListener(type,handler,false)}else if(element.attachEvent){element.attachEvent('on'+type,handler)}else{element['on'+type]=handler}},getWheelDelta:function(event){return event.wheelDelta?event.wheelDelta:(-event.detail)*40}},mouseHandle=function(event){event=eventHandle.getEvent(event);var delta=eventHandle.getWheelDelta(event);if(delta<0)"rtl"===a.package.metadata.direction?h.prev():h.next();else"rtl"===a.package.metadata.direction?h.next():h.prev()};return(document.addEventListener("keydown",p,!1),eventHandle.addEvent(document,'mousewheel',mouseHandle),eventHandle.addEvent(document,'DOMMouseScroll',mouseHandle)
```

# 尾声

提了个 Pull Request: https://github.com/janeczku/calibre-web/pull/2109

不过理所当然被拒啦（毕竟是侵入性的）
