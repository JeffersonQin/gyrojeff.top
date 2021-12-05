---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-12-05 22:11:36'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-12-05 22:11:36'
parent: 0
password: ''
slug: wpf-mvvm-complex-notify
status: publish
tags: [.NET, C#, MVVM, WPF]
template: ''
title: WPF MVVM 多级数据绑定的 Notify
type: post
---
# 问题引入

这个标题我觉得起的也不是最好，因为很难概括我想要表达的意思。接下来我就用两个例子来引入吧。

## Ex.1

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211205222011.png)

* A 有属性 B1
* B1 有属性 C1
* C1 有属性 D1
* 现在 D1 改变了，我们希望 A, B1, C1 都被 Notify

## Ex.2

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211205222758.png)

* `ObservableCollection<T>` 被绑定到了一个集合的 DataContext
* T 有属性 A1
* A1 有属性 B1
* B1 有属性 C1
* C1 有属性 D
* D 改变了，想让 T 被 Notify

# 解决方案

对于每一个 `Class`，都设置一个 `Dummy` 属性，作用就是被 `Notify`。在数据绑定的时候就用一个 `MultiValueConverter`，绑定我们要绑定的数据本身和 `Dummy`，这样就既能够获取数据，又能够被 `Notify`。

对于上面 `Chain` 一样的东西，我引入了一些黑魔法：

```C#
[AttributeUsage(AttributeTargets.Property)]
public class ChainNotifiable : Attribute {}

[AttributeUsage(AttributeTargets.Class, AllowMultiple = false, Inherited = false)]
public class AncestorNotifiableMarker : Attribute
{
	public string AncestorName { get; set; }

	public AncestorNotifiableMarker(string ancestorName)
	{
		AncestorName = ancestorName;
	}
}

public class ChainNotifiableObject : NotifyObject // NotifyObject 是我自己实现的一个 INotifyPropertyChanged 的类
{
	public object DummyObject { get; set; }
	
	public override void RaisePropertyChanged([CallerMemberName] string propertyName = "")
	{
		base.RaisePropertyChanged(propertyName);
		
		var attribute = GetType().GetProperty(propertyName)?.GetCustomAttribute<ChainNotifiable>();
		if (attribute == null) return;
		
		base.RaisePropertyChanged(nameof(DummyObject));

		var type = GetType();
		var obj = this as object;
		while (true)
		{
			var ancestorMarker = type.GetCustomAttribute<AncestorNotifiableMarker>();
			if (ancestorMarker == null) return;
			var field = type.GetProperty(ancestorMarker.AncestorName);
			obj = field.GetValue(obj);
			type = field.GetType();
			((NotifyObject)obj).RaisePropertyChanged("DummyObject");
		}
	}
}
```

说一下我做了什么：
* 我定义了一个 `Attribute`，可以用来修饰 `Property`，注明如果这个 `Property` 改变了，会不会触发 `Chain` 的 `Notify`
* 有定义了一个 `Attribute`，用来修饰 `Class`，用来注明这个 `Class` 的 `Parent Property` 的名字。
* 继承了 `NotifyObject`，定义了一个 `ChainNotifiableObject`，重载了 `RaisePropertyChanged`，通过反射实现 `Chain` 形式的 `Notify`。

# 使用

`ViewModel`:

```C#
public class ClassA : ChainNotifiableObject
{	
	private ClassB _valueB = false;

	[ChainNotifiable]
	public ClassB ValueB
	{
		get => _valueB;
		set => SetProperty(ref _valueB, value);
	}

	// Other properties
}

[AncestorNotifiableMarker("Parent")]
public class ClassB : ChainNotifiableObject
{
	private ClassA _parent = false;

	[ChainNotifiable]
	public ClassA Parent
	{
		get => _parent;
		set => SetProperty(ref _parent, value);
	}

	// Other properties
}
```

`XAML`:

```xml
<Button>
	<Button.Content>
		<MultiBinding Converter="WhateverConverter">
			<Binding Path="ValueA">
			<Binding Path="ValueA.DummyObject">
		</MultiBinding>
	</Button.Content>
</Button>
```

或者是 `ObservableCollection` 的情况：

```xml
<ItemsControl ItemsSource="{Binding WhateverBinding}">
	<ItemsControl.ItemsPanel>
		<ItemsPanelTemplate>
			<Canvas/>
		</ItemsPanelTemplate>
	</ItemsControl.ItemsPanel>
	<ItemsControl.ItemTemplate>
		<DataTemplate>
			<Button>
				<Button.Content>
					<MultiBinding Converter="WhateverConverter">
						<Binding/>
						<Binding Path="DummyObject"/>
					</MultiBinding>
				</Button.Content>
			</Button>
		</DataTemplate>
	</ItemsControl.ItemTemplate>
</ItemsControl>
```
