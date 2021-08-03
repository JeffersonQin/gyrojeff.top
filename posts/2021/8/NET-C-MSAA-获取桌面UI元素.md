---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-03 23:46:31'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-03 23:46:31'
parent: 0
password: ''
slug: NET-C-MSAA-获取桌面UI元素
status: publish
tags: [.NET, C#]
template: ''
title: .NET C# MSAA 获取桌面UI元素
type: post
---
和Snipaste开发交流之后，觉得还是不死心，心里过不去，于是乎又拿MSAA ()

非常有用的文章：
- https://www.codeproject.com/Articles/38906/UI-Automation-Using-Microsoft-Active-Accessibility
- https://stackoverflow.com/questions/27323110/msaa-finds-controls-ui-automation-doesnt

官方`doc`:
- MSAA和UIAutomation比较: https://docs.microsoft.com/en-us/windows/win32/winauto/microsoft-active-accessibility-and-ui-automation-compared
- API: https://docs.microsoft.com/en-us/windows/win32/api/oleacc/

主要使用的方法：
- https://docs.microsoft.com/en-us/windows/win32/api/oleacc/nf-oleacc-accessiblechildren
- https://docs.microsoft.com/en-us/windows/win32/api/oleacc/nf-oleacc-accessibleobjectfromwindow
- https://docs.microsoft.com/en-us/windows/win32/api/oleacc/nn-oleacc-iaccessible

注意：这块`VS`的代码提示可能会有缺失，一切以`demo code`和API为准。

这里我找到了一个九年前的项目，其中包含了相关的代码，可以借鉴：
- https://github.com/PragyaPande/EnableFFPlugin

我怕没了所以又fork了一份🤣：
- https://github.com/JeffersonQin/EnableFFPlugin

注意：这个项目有些部分代码是有问题的，比方说`if (xxxObject != null && xxxObject.Trim() != "")`，这种写法明显如果出了`NullPointerException`就挂。所以大家还是要有分辨能力。

最终，下面是我的Win32API P/Invoke (有些没用到)：

```C#
[DllImport("oleacc.dll")]
public static extern uint GetRoleText(uint dwRole, [Out] StringBuilder lpszRole, uint cchRoleMax);

[DllImport("oleacc.dll")]
public static extern uint GetStateText(uint dwStateBit, [Out] StringBuilder lpszStateBit, uint cchStateBitMax);

[DllImport("oleacc.dll")]
public static extern uint WindowFromAccessibleObject(IAccessible pacc, ref IntPtr phwnd);

[DllImport("oleacc.dll", PreserveSig = false)]
[return: MarshalAs(UnmanagedType.Interface)]
public static extern object AccessibleObjectFromWindow(int hwnd, int dwId, ref Guid riid);

[DllImport("oleacc.dll")]
public static extern int AccessibleObjectFromWindow(IntPtr hwnd, uint id, ref Guid iid, [In, Out, MarshalAs(UnmanagedType.IUnknown)] ref object ppvObject);

[DllImport("oleacc.dll")]
public static extern int AccessibleChildren(IAccessible paccContainer, int iChildStart, int cChildren, [Out()] [MarshalAs(UnmanagedType.LPArray, SizeParamIndex = 4)] object[] rgvarChildren, ref int pcObtained);
```

抽出的一些常量 (很多都位于 `WinUser.h`)：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804004646.png)

```C#
public enum ObjectIdentifiers : uint
{
	OBJID_WINDOW            = 0x00000000,
	OBJID_SYSMENU           = 0xFFFFFFFF,
	OBJID_TITLEBAR          = 0xFFFFFFFE,
	OBJID_MENU              = 0xFFFFFFFD,
	OBJID_CLIENT            = 0xFFFFFFFC,
	OBJID_VSCROLL           = 0xFFFFFFFB,
	OBJID_HSCROLL           = 0xFFFFFFFA,
	OBJID_SIZEGRIP          = 0xFFFFFFF9,
	OBJID_CARET             = 0xFFFFFFF8,
	OBJID_CURSOR            = 0xFFFFFFF7,
	OBJID_ALERT             = 0xFFFFFFF6,
	OBJID_SOUND             = 0xFFFFFFF5,
	OBJID_QUERYCLASSNAMEIDX = 0xFFFFFFF4,
	OBJID_NATIVEOM          = 0xFFFFFFF0,
	CHILDID_SELF            = 0
}

public static class ReferenceIdentifiers
{
	public static Guid IID_IUnknown = new Guid(0x00000000, 0x0000, 0x0000, 0xc0, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x46);
	public static Guid IID_IDispatch = new Guid(0x00020400, 0x0000, 0x0000, 0xC0, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x46);
	public static Guid IID_IAccessible = new Guid(0x618736e0, 0x3c3d, 0x11cf, 0x81, 0x0c, 0x00, 0xaa, 0x00, 0x38, 0x9b, 0x71);
}
```

实现：

```C#
public static class GMSAAManager
{

	public static String GetElementName(IAccessible element)
	{
		try
		{
			return element.get_accName((int)ObjectIdentifiers.CHILDID_SELF);
		}
		catch (Exception)
		{
			return "";
		}
	}


	public static Rect GetElementBounds(IAccessible element)
	{
		try
		{
			element.accLocation(out int pxLeft, out int pyTop, out int pcxWidth, out int pcyHeight, (int)ObjectIdentifiers.CHILDID_SELF);
			return new Rect(pxLeft, pyTop, pcxWidth, pcyHeight);
		}
		catch (Exception)
		{
			return new Rect(0, 0, 0, 0);
		}
	}


	public static IAccessible[] GetChildren(IAccessible element)
	{
		try
		{
			IAccessible[] children = new IAccessible[element.accChildCount];
			int obtained = 0;
			Win32API.AccessibleChildren(element, 0, element.accChildCount, children, ref obtained);
			if (obtained == 0) return null;
			return children;
		}
		catch (Exception)
		{
			return null;
		}
	}


	public static GUIElement GetElement(IAccessible element)
	{
		return new GUIElement(GetElementBounds(element), GetElementName(element));
	}


	public static GUIElement GetElement(IAccessible element, Rect bounds)
	{
		return new GUIElement(bounds, GetElementName(element));
	}
	
	public static List<GUIElement> GetLeafElements(IAccessible element)
	{
		List<GUIElement> res = new List<GUIElement>();
		try
		{
			Rect bounds = GetElementBounds(element);
			if (!JudgeBounding(bounds)) return res;
			GetLeafElementsDFS(element, res, bounds);
		}
		catch (Exception)
		{
			return res;
		}
		return res;
	}


	public static IAccessible GetWindow(IntPtr hWnd)
	{
		object obj = null;
		Win32API.AccessibleObjectFromWindow(hWnd, (uint)ObjectIdentifiers.OBJID_WINDOW, ref ReferenceIdentifiers.IID_IAccessible, ref obj);
		return (IAccessible)obj;
	}


	private static bool JudgeBounding(Rect bounds)
	{
		try
		{
			if (bounds.Width == 0 || bounds.Height == 0)
				return false;
			return !bounds.IsEmpty;
		}
		catch (Exception)
		{
			return false;
		}
	}


	private static void GetLeafElementsDFS(IAccessible element, List<GUIElement> res, Rect bounds)
	{
		var children = GetChildren(element);
		if (children == null)
		{
			res.Add(GetElement(element, bounds));
			return;
		}
		if (children.Length == 0)
		{
			res.Add(GetElement(element, bounds));
			return;
		}
		foreach (var child in children)
		{
			if (child == null) continue;
			if (!child.GetType().IsCOMObject) continue;
			Rect rect = GetElementBounds(child);
			if (!JudgeBounding(rect)) continue;
			if (rect.Bottom <= bounds.Top || rect.Right <= bounds.Left) continue;
			if (rect.Top >= bounds.Bottom || rect.Left >= bounds.Right) continue;
			GetLeafElementsDFS(child, res, rect);
		}
	}
	
}
```

注意: 在这个过程中一定要注意类型问题。特别是`C++`的类型和`C#`之间对应的问题。我在这里就不列了，记得文档里用到什么就去查类型，有的时候该强制转换就强制转换。还有，`C#`的`Enum`后面就算跟了类型也没用，也要强制转换。总而言之，类型问题基本上贡献了这块90%的Bug（大概），所以一定要谨慎。

结论：比`UIAutomation`还要慢，基本上可以实锤是`FFI` (跨语言调用) 的锅了，过两天拿`C++`试试看。
