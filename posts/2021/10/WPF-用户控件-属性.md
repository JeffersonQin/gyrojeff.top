---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-10-04 11:31:18'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-10-04 11:31:18'
parent: 0
password: ''
slug: WPF-properties-of-user-controls
status: publish
tags: [.NET, C#]
template: ''
title: WPF 用户控件 属性
type: post
---
1. 将 `DataContext` 设置为本身：<br>
   在 `XAML` 中设置
   ```xml
   <UserControl DataContext="{Binding RelativeSource={RelativeSource Self}}">
   ```
   或者在后台设置，二选一
   ```c#
	public <UserControl>()
	{
		InitializeComponent();
		DataContext = this;
	}
   ```
2. 形如下面的代码就能创建 UserControl 的自定义属性：
   ```c#
	public int ContentWidth
	{
		get { return (int)GetValue(ContentWidthProperty); }
		set { SetValue(ContentWidthProperty, value); }
	}

	// Using a DependencyProperty as the backing store for ContentWidth.  This enables animation, styling, binding, etc...
	public static readonly DependencyProperty ContentWidthProperty =
		DependencyProperty.Register("ContentWidth", typeof(int), typeof(CommonZoomView), new PropertyMetadata(0));
   ```
3. 在 `XAML` 中使用:
   ```xml
   <xxx Width="{Binding ContentWidth}">
   ```


技巧：在 Visual Studio 中输入 `propdp` 就能快速调用这个 Snippet

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211121130630.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211121130758.png)
