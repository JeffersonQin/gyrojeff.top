---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-10-04 11:33:21'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-10-04 11:33:21'
parent: 0
password: ''
slug: wpf-use-user-control-as-container
status: publish
tags: [.NET, C#]
template: ''
title: WPF 用户控件作为容器
type: post
---
# 把 `UIElement` 作为属性

关于 UserControl 的自定义属性，参考上一篇文章。我们这里创建一个类型为 `UIElement` 的属性：

```c#
public UIElement ContainerContent
{
	get { return (UIElement)GetValue(ContainerContentProperty); }
	set { SetValue(ContainerContentProperty, value); }
}

// Using a DependencyProperty as the backing store for ContainerContent.  This enables animation, styling, binding, etc...
public static readonly DependencyProperty ContainerContentProperty =
	DependencyProperty.Register("ContainerContent", typeof(UIElement), typeof(CommonZoomView), new PropertyMetadata(null));
```

在 `XAML` 中使用 (这里的 `ContentWidth`, `ContentHeight` 啥的我都没写，意会一下)：

```xml
<ContentControl x:Name="ContentControlInstance" Width="{Binding ContentWidth}" Height="{Binding ContentHeight}">
	<ContentPresenter Content="{Binding ContainerContent}"></ContentPresenter>
</ContentControl>
```

# 在其他地方使用

首先定义一个 `namespace`, 我叫他 `uc`, 就是那个控件所在的位置:

```xml
<Window
    ...
    xmlns:uc="clr-namespace:YuzuMarker.Control"
	...
>
```

然后在某个地方使用

```xml
<uc:CommonZoomView>
	<uc:CommonZoomView.ContainerContent>
		<!--这里就是 Container 内部了-->
	</uc:CommonZoomView.ContainerContent>
</uc:CommonZoomView>
```

# 关于数据绑定的坑

由于我们定义用户控件的时候把 DataContext 切换成了控件本身，那么如果在 `Container` 内部想要绑定 `Container` 外部的数据就成了难题。

这个时候我们引入一个 `BindingProxy`。

首先新建一个 `BindingProxy` 的 `class`: 

```c#
/// <summary>
/// Add Proxy &lt;ut:BindingProxy x:Key="Proxy" Data="{Binding}" /&gt; to Resources <br/>
/// Bind like &lt;Element Property="{Binding Data.MyValue, Source={StaticResource Proxy}}" /&gt; <br/>
/// From: https://stackoverflow.com/questions/6575180/how-to-access-parents-datacontext-from-a-usercontrol
/// </summary>
public class BindingProxy : Freezable
{
	protected override Freezable CreateInstanceCore()
	{
		return new BindingProxy();
	}

	public object Data
	{
		get { return (object)GetValue(DataProperty); }
		set { SetValue(DataProperty, value); }
	}

	public static readonly DependencyProperty DataProperty = DependencyProperty.Register("Data", typeof(object), typeof(BindingProxy));
}
```

然后在用户控件中调用这个 `Proxy`: (`c:BindingProxy` 的 `c` 是 `namespace`, 自己创建一下)

```xml
<UserControl.Resources>
	<c:BindingProxy x:Key="BindingProxy" Data="{Binding RelativeSource={RelativeSource FindAncestor, AncestorType={x:Type Window}},Path=DataContext}" />
</UserControl.Resources>
```

上述代码的意思是把类型是 `Window` 的 UI Tree 上的祖先的 `DataContext` 绑定到 `UserControl` 上。现在基本上就大功告成了。

但是，`Container` 内部进行数据绑定的时候要更改一下表达式，例子：

原来：
```xml
"{Binding ImageSource, Mode=OneWay"
```

修改为：

```xml
"{Binding Data.ImageSource, Mode=OneWay, Source={StaticResource BindingProxy}}"
```
