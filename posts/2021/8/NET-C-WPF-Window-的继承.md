---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-06 15:39:40'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-06 15:39:40'
parent: 0
password: ''
slug: NET-C-WPF-Window-的继承
status: publish
tags: [.NET, C#]
template: ''
title: .NET C# WPF Window 的继承
type: post
---
直接上范例：

```C#
public class MaskWindow : ReuseWindow
{
	public MaskWindow() : base()
	{
		WindowStyle = System.Windows.WindowStyle.None;
		AllowsTransparency = true;
		ShowInTaskbar = false;
		ResizeMode = System.Windows.ResizeMode.NoResize;
		Title = "MaskWindow";
		Background = new SolidColorBrush(Color.FromArgb(40, 0, 0, 0));
		MouseDown += Window_MouseDown;
		KeyDown += Window_KeyDown;
	}

	private void Window_MouseDown(object sender, MouseButtonEventArgs e)
	{
		WindowManager.FocusSearch();
	}

	private void Window_KeyDown(object sender, KeyEventArgs e)
	{
		if (e.Key == Key.Escape)
		{
			new Thread(() =>
			{
				WindowManager.EndProcess();
			}).Start();
		}
	}
}
```

使用的时候：

```C#
public partial class ScreenMaskWindow : MaskWindow
{
	public ScreenMaskWindow()
	{
		InitializeComponent();
	}
}
```

```XAML
<local:MaskWindow x:Class="Ayase.UI.ScreenMaskWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:Ayase.UI"
        mc:Ignorable="d"
        Activated="MaskWindow_Activated"
        Title="ScreenMaskWindow" Height="450" Width="800">
    <Grid>
        
    </Grid>
</local:MaskWindow>
```

最外层标签从原来的`Window`改为了`local:MaskWindow`（即自定义的类），这里`IDE`会自动提示的，无需担心。
