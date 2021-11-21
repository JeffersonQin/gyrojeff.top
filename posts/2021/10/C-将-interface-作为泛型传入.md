---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-10-04 16:52:26'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/92191625_p1_master1200.jpg',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-10-04 16:52:26'
parent: 0
password: ''
slug: pass-interface-into-class-or-methods-in-csharp
status: publish
tags: [C#]
template: ''
title: C# 将 interface 作为泛型传入
type: post
---
可以有如下操作：

```C#
public class A<B> where B : IList<int>, new() {

}
```

甚至还可以多重嵌套：

```C#
public class A<B, C> where B: IList<C>, new() where C: IList<int>, new() {

}
```

注意：
* 这里的 `int` 是举例子，可以替换成其他数据类型。
* 如果传入 `A` 作为其他方法的参数，也需要在后面跟上那一长串。
* `new()` 是为了生命可以通过 `new B()` 来初始化对象，是一种约束
* `C: IList<int>, new()` 说明 `C` 即服从上一条能够通过 `new C()` 初始化对象，也是 `IList<int>`的子类。
