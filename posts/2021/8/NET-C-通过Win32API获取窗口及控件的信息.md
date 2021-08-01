---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-02 00:36:57'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-02 00:36:57'
parent: 0
password: ''
slug: NET-C-通过Win32API获取窗口及控件的信息
status: publish
tags: [.NET C#]
template: ''
title: .NET C# 通过Win32API获取窗口及控件的信息
type: post
---
这篇文章同样是想要继续尝试加速`UI Tree`的获取...不说别的了，直接说怎么实现吧。

# 过程

## 对用得上的`Win32API`进行封装

值得注意的是：官方文档`C++`中的`LONG`，在`C#`中是`int`，麻了。

```C#
public class Win32API
{

	public struct RECT
	{
		public int Left;
		public int Top;
		public int Right;
		public int Bottom;
	}
	
	
	/// <summary>
	/// Find the hWnd of the window by ClassName or WindowName
	/// </summary>
	/// <param name="lpClassName">ClassName of window</param>
	/// <param name="lpWindowName">WindowName of window</param>
	/// <returns></returns>
	[DllImport("User32.dll", EntryPoint = "FindWindow")]
	public static extern IntPtr FindWindow(string lpClassName, string lpWindowName);


	/// <summary>
	/// Find the hWnd of the window
	/// </summary>
	/// <param name="hwndParent">Parent hWnd</param>
	/// <param name="hwndChildAfter">hWnd of last sibling</param>
	/// <param name="lpszClass">ClassName of Window</param>
	/// <param name="lpszWindow">WindowName of Window</param>
	/// <returns></returns>
	[DllImport("user32.dll", EntryPoint = "FindWindowEx")]
	public static extern IntPtr FindWindowEx(IntPtr hwndParent, IntPtr hwndChildAfter, string lpszClass, string lpszWindow);



	[DllImport("user32.dll", SetLastError = true)]
	public static extern int GetWindowTextLength(IntPtr hWnd);


	[DllImport("User32.dll", CharSet = CharSet.Auto, SetLastError = true)]
	public static extern int GetWindowText(IntPtr hWnd, StringBuilder text, int nMaxCount);


	[DllImport("user32.dll")]
	public static extern int GetWindowRect(IntPtr hwnd, out RECT lpRect);
}
```

## 抽象一个包含位置和名称的类

```C#
public class GUIElement
{
	public Rect BoundingRectangle = new Rect(0, 0, 0, 0);
	public String Name = "";

	
	public GUIElement(Rect rect)
	{
		BoundingRectangle = rect;
	}

	public GUIElement(String name)
	{
		Name = name;
	}


	public GUIElement(Rect rect, String name)
	{
		BoundingRectangle = rect;
		Name = name;
	}
}
```

## 火速写一个`DFS`

这次还没写并发，主要是为了验证功能。

```C#
public static class GRawUIManager
{

	public static String GetElementName(IntPtr hWnd)
	{
		int length = Win32API.GetWindowTextLength(hWnd);
		StringBuilder name = new StringBuilder(length);
		Win32API.GetWindowText(hWnd, name, name.Capacity);
		return name.ToString();
	}


	public static Rect GetElementBounds(IntPtr hWnd)
	{
		Win32API.GetWindowRect(hWnd, out Win32API.RECT rect);
		return new Rect(new Point(rect.Left, rect.Top), new Point(rect.Right, rect.Bottom));
	}


	public static GUIElement GetUIElement(IntPtr hWnd)
	{
		return new GUIElement(GetElementBounds(hWnd), GetElementName(hWnd));
	}


	public static List<GUIElement> GetLeafElements(IntPtr hWnd)
	{
		List<GUIElement> res = new List<GUIElement>();
		try
		{
			Rect bounds = GetElementBounds(hWnd);
			if (!JudgeBounding(bounds)) return res;
			GetLeafElementsDFS(hWnd, res, bounds);
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
			return true;
		}
		catch (Exception)
		{
			return false;
		}
	}


	private static void GetLeafElementsDFS(IntPtr hWnd, List<GUIElement> res, Rect bounds)
	{
		IntPtr childHWnd = IntPtr.Zero;
		int child_cnt = 0;
		while (true)
		{
			childHWnd = Win32API.FindWindowEx(hWnd, childHWnd, null, null);
			if (childHWnd == IntPtr.Zero) break;
			child_cnt ++;
			Rect rect = GetElementBounds(childHWnd);
			if (!JudgeBounding(rect)) continue;
			if (rect.Bottom <= bounds.Top || rect.Right <= bounds.Left) continue;
			if (rect.Top >= bounds.Bottom || rect.Left >= bounds.Right) continue;
			GetLeafElementsDFS(childHWnd, res, rect);
		}
		if (child_cnt == 0) res.Add(GetUIElement(hWnd));
	}
}
```

# 测试与结果

跑了一下，速度：很快，快上天了。但是一看输出：这都是啥和啥呀（悲）。大部分窗口只能获取最上面的句柄，只有少量窗口还能搞出几个控件（悲）。所以总结一下，基本上是没法用了，估计还得爬回`UIAutomation`.

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210802004338.png)

↑：速度很快但啥都没有...
