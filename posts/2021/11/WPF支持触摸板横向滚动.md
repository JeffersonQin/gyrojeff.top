---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-11-22 12:39:06'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-11-22 12:39:06'
parent: 0
password: ''
slug: wpf-handle-horizontal-scrolling-of-touchpad
status: publish
tags: [.NET, C#, WPF]
template: ''
title: WPF支持触摸板横向滚动
type: post
---
* 修改自：https://blog.walterlv.com/post/handle-horizontal-scrolling-of-touchpad.html
* 原文作者：吕毅

---

微软终于开始学苹果一样好好做触摸板了（就是键盘空格键下面那一大块）。然而鉴于以前没有好好做，以至于 WPF 程序甚至都没有对触摸板的横向滚动提供支持（竖向滚动是直接使用了 MouseWheel，汗……）。但有些功能真希望能够支持横向滚动！

本文将介绍让触摸板支持横向滚动的方法，本质上也是用 MouseWheel，但却支持了横向。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211122124205.png)

我们需要从 `Windows` 的窗口消息中获取 `WM_MOUSEHWHEEL` 消息。对，就是鼠标滚轮消息！以前我们只取了纵向数据，现在我们要取横向数据。

首先，我们需要能够监听得到消息才行。重写 `Window` 的 `OnSourceInitialized` 方法可以开始监听消息；如果代码没办法写到 `Window` 中，可以通过 `Window.GetWindow(DependencyObject)` 获取到窗口实例后监听它的 `SourceInitialized` 事件。如果拿不到这样的时机，则只要在任何 `SourceInitialized` 之后的时机（比如 `Loaded` ）都可以写下面方法内部的两行代码。

```C#
protected override void OnSourceInitialized(EventArgs e)
{
    var source = PresentationSource.FromVisual(_board);
    ((HwndSource) source)?.AddHook(Hook);
}

private IntPtr Hook(IntPtr hwnd, int msg, IntPtr wParam, IntPtr lParam, ref bool handled)
{
    // 在这里添加消息的处理。
    return IntPtr.Zero;
}
```

接下来，我们开始处理 `WM_MOUSEHWHEEL`：

```C#
const int WM_MOUSEHWHEEL = 0x020E;

private IntPtr Hook(IntPtr hwnd, int msg, IntPtr wParam, IntPtr lParam, ref bool handled)
{
    switch (msg)
    {
        case WM_MOUSEHWHEEL:
            int tilt = (short) HIWORD(wParam);
            OnMouseTilt(tilt);
            return (IntPtr) 1;
    }
    return IntPtr.Zero;
}

/// <summary>
/// 取指针所在高位数值。
/// </summary>
private static int HIWORD(IntPtr ptr)
{
    var val32 = ptr.ToInt32();
    return ((val32 >> 16) & 0xFFFF);
}

/// <summary>
/// 取指针所在低位数值。
/// </summary>
private static int LOWORD(IntPtr ptr)
{
    var val32 = ptr.ToInt32();
    return (val32 & 0xFFFF);
}

private void OnMouseTilt(int tilt)
{
    // 这里就是触摸板横向滚动的时机，参数是横向滚动的数值，就像鼠标滚轮纵向滚动的数值一样。
}
```

`OnMouseTilt` 中就可以写我们触摸板横向滚动的处理代码。

以上代码都可以封装成通用的方法，在 `OnMouseTilt` 中抛出一个类似于 `MouseWheel` 一样的事件是非常好的选择。

微软的 `Microsoft Sculpt Comfort Mouse` 鼠标滚轮也是支持横向滚动的，以上方法也可以支持。

---

但是，原文关于取 `LOWORD` 和 `HIWORD` 有问题，在 64 位系统上，反向滚动会造成 Arithmetic Overflow, 所以下面是修改后的代码：

```C#
/// <summary>
/// 取指针所在高位数值。
/// </summary>
private static int HIWORD(IntPtr ptr)
{
    unchecked
    {
        if (Environment.Is64BitOperatingSystem)
        {
            var val64 = ptr.ToInt64();
            return (short) ((val64 >> 16) & 0xFFFF);
        }
        var val32 = ptr.ToInt32();
        return (short) ((val32 >> 16) & 0xFFFF);   
    }
}

/// <summary>
/// 取指针所在低位数值。
/// </summary>
private static int LOWORD(IntPtr ptr)
{
    unchecked
    {
        if (Environment.Is64BitOperatingSystem)
        {
            var val64 = ptr.ToInt64();
            return (short)(val64 & 0xFFFF);
        }

        var val32 = ptr.ToInt32();
        return (short)(val32 & 0xFFFF);
    }
}
```

下面一段代码是评论中[t3brown](https://github.com/t3brown)的评论，可以获取鼠标所在位置的滚动视图并进行滚动，十分方便：

```C#
private void OnMouseTilt(int tilt)
{
	UIElement element = Mouse.DirectlyOver as UIElement;

	if (element == null) return;

	ScrollViewer scrollViewer = element is ScrollViewer viewer ? viewer : FindParent<ScrollViewer>(element);

	if (scrollViewer == null)
		return;

	scrollViewer.ScrollToHorizontalOffset(scrollViewer.HorizontalOffset + tilt);
}

public static T FindParent<T>(DependencyObject child) where T : DependencyObject
{
	DependencyObject parentObject = VisualTreeHelper.GetParent(child);

	if (parentObject == null) return null;

	T parent = parentObject as T;
	if (parent != null)
		return parent;
	else
		return FindParent<T>(parentObject);
}
```
