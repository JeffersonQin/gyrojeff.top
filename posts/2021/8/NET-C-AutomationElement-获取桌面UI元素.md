---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-01 11:13:44'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/af3de31445dbde9baeef94903efb39c.jpg',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-01 11:13:44'
parent: 0
password: ''
slug: NET-C-AutomationElement-获取桌面UI元素
status: publish
tags: [.NET, C#]
template: ''
title: .NET C# AutomationElement 获取桌面UI元素
type: post
---
这个东西其实是去年9月研究的，不过当时项目烂尾了，现在准备重启这个项目，所以特此写文章记录。项目地址：https://github.com/JeffersonQin/Ayase

至于为什么要用这个名字命名...我永远喜欢三司绫濑（~~零奶~~）！角色命名，从我做起。

好了，不说题外话了。`AutomationElement`是微软提供的用于软件自动化测试的接口，这里讲一下怎么用。

# 用法

## 获取根元素

```C#
AutomationElement.RootElement;
```

## 通过一个元素获取其他元素

注意，我们要掌握一个核心思想，那就是所有的UI元素构成一颗**树**，所以有：`Children` (儿子), `Parent` (父亲), `Descendent` (后代), 等图论中常说的东西。

我们可以指定一个范围和条件，搜索其他元素，比如：

```
return element.FindFirst(TreeScope.Children,
    new PropertyCondition(AutomationElement.ProcessIdProperty, 54376));
```

这句话的意思就是找到`element`元素**儿子节点**中`ProcessID`是54376的元素。

具体的用法可以查看官方文档：https://docs.microsoft.com/en-us/dotnet/api/system.windows.automation?view=net-5.0

里面详细讲述了如何获取各个属性

## 获取一个元素子树所有的终止节点

实现一个DFS即可，下面是我自己写的实现 (其中要求元素的都在屏幕的可见范围内，所以加了一个`JudgeBounding`)：

```C#
/// <summary>
/// Get the elements that don't have children of an element
/// </summary>
/// <param name="element">Input element</param>
/// <returns></returns>
public static List<AutomationElement> GetEndElements(AutomationElement element)
{
	List<AutomationElement> res = new List<AutomationElement>();
	try
	{
		Rect bounds = element.Current.BoundingRectangle;
		if (!JudgeBounding(bounds)) return res;
		GetEndElementsDFS(element, res, bounds);
	}
	catch (Exception)
	{
		return res;
	}
	return res;
}


private static bool JudgeBounding(Rect bounds)
{
	try
	{
		if (bounds == null)
			return false;
		if (bounds.Width == 0 || bounds.Height == 0)
			return false;
		if (bounds.IsEmpty)
			return false;
		if (double.IsInfinity(bounds.Top) ||
			double.IsInfinity(bounds.Left) ||
			double.IsInfinity(bounds.Width) ||
			double.IsInfinity(bounds.Height))
			return false;
		return true;
	}
	catch (Exception)
	{
		return false;
	}
}


private static void GetEndElementsDFS(AutomationElement element, List<AutomationElement> res, Rect bounds)
{
	AutomationElementCollection children = element.FindAll(TreeScope.Children,
		new PropertyCondition(AutomationElement.IsOffscreenProperty, false));
	foreach (AutomationElement e in children)
	{
		try
		{
			Rect rect = e.Current.BoundingRectangle;
			if (!JudgeBounding(rect)) continue;
			if (rect.Bottom <= bounds.Top || rect.Right <= bounds.Left) continue;
			if (rect.Top >= bounds.Bottom || rect.Left >= bounds.Right) continue;
			GetEndElementsDFS(e, res, rect);
		} catch (Exception)
		{
			continue;
		}
	}
	if (children.Count == 0) res.Add(element);
}
```

## 获取元素的名称、边框等

```C#
Rect boundings = element.Current.BoundingRectangle;
String name = element.Current.Name;
```

# `AutomationElement`的弊端

性能太他妈烂了。之后几篇文章会讲讲我对性能优化的探索。
