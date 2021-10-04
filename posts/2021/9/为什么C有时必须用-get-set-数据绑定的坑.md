---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-09-30 01:26:28'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211004112948.png',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-10-04 11:30:03'
parent: 0
password: ''
slug: 为什么C有时必须用-get-set-数据绑定的坑
status: publish
tags: [.NET, C#, WPF]
template: ''
title: 为什么C#有时必须用{ get; set; } - 数据绑定的坑
type: post
---
Stackoverflow:

https://stackoverflow.com/questions/5096926/what-is-the-get-set-syntax-in-c

> Just to add something... If you don't put `{get; set;}` you are creating a Field but if you put the `{get; set;}` you are creating a Property. Having a property could make some things easier especially when working with Reflection.

如果加了 `{get; set;}` 那么在创建 `Property`，如果不加就是在创建 `Field`。`Field` 在 `Data Binding` 的时候不会起作用，必须要用 `Property`。
