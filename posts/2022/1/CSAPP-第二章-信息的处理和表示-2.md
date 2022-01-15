---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-09-03 21:55:51'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210903214801.png', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-09-03 21:55:51'
parent: 0
password: ''
slug: CSAPP-第二章-信息的处理和表示-2
status: publish
tags: [CSAPP]
template: ''
title: CSAPP | 第二章 信息的处理和表示 (2)
type: post
---
# Chapter 2 : 信息的表示和处理 - 习题 (2)

## 勘误

P68

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/728944797e28a908b2600adb9d9eb6b.png)

P73

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210903222354.png)

## 2.27

```c
int uadd_ok(unsigned x, unsigned y) {
	unsigned sum = x + y;
	return sum >= x;
}
```

## 2.28

```C
int tadd_ok(int x, int y) {
	int sum = x + y;
	if (x > 0 && y > 0 && sum < 0) return 0;
	if (x < 0 && y < 0 && sum > 0) return 0;
	return 1;
}
```
