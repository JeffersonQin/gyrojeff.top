---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-11-30 16:38:35'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-11-30 16:38:35'
parent: 0
password: ''
slug: csharp-use-custom-attribute-to-optimize-undo-redo-manager
status: publish
tags: [.NET, C#]
template: ''
title: C# 用 Custom Attribute 优化撤消重做栈的属性
type: post
---
在之前的文章中讲过如何实现撤消重做栈：https://gyrojeff.top/index.php/archives/wpf-implement-undo-redo/

# 问题引入

之前，我们每定义一个支持撤消重做的属性都要写很多代码：

```C#
private string _text;

public string Text
{
	get => _text;
	set => UndoRedoManager.PushAndPerformRecord(o =>
	{
		var nowValue = Text;
		SetProperty(ref _text, (string)o);
		return nowValue;
	}, o =>
	{
		var nowValue = Text;
		o ??= value;
		SetProperty(ref _text, (string)o);
		return nowValue;
	});
}
```

本文的目标是将上述冗长的表达改写成：

```C#
private string _text;

[Undoable]
public string Text
{
	get => _text;
	set => SetProperty(value);
}
```

# 实现逻辑

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/YuzuMarker-Undo-Redo-Attribtue.png)

# 代码

```C#
public enum BackingNamingStyle
{
	/// <summary>
	/// Example: "BackingStore" => "_backingStore"
	/// </summary>
	UnderscoreAndLowerCase,
	/// <summary>
	/// Example: "BackingStore" => "backingStore"
	/// </summary>
	LowerCase
}

public partial class NotifyObject
{
	public bool SetProperty<T>(T value, [CallerMemberName] string propertyName = "", 
		BackingNamingStyle backingNamingStyle = BackingNamingStyle.UnderscoreAndLowerCase,
		Action beforeChanged = null, Action onChanged = null,
		UndoRedoRecord.DelegateActionWithAndReturnValue undoAction = null,
		UndoRedoRecord.DelegateActionWithAndReturnValue redoAction = null,
		UndoRedoRecord.DelegateActionWithValue disposeAction = null)
	{
		var backingName = "";
		switch (backingNamingStyle)
		{
			case BackingNamingStyle.LowerCase:
				backingName = propertyName[0].ToString().ToLower() + propertyName.Substring(1);
				break;
			case BackingNamingStyle.UnderscoreAndLowerCase:
				backingName = "_" + propertyName[0].ToString().ToLower() + propertyName.Substring(1);
				break;
		}
		
		Type type = null;
		FieldInfo backingInstance = null;
		do
		{
			type = type == null ? GetType() : type.BaseType;
			if (type == null) return false;
			backingInstance = type.GetField(backingName, BindingFlags.NonPublic | BindingFlags.Instance);
		} while (backingInstance == null);
		
		if (EqualityComparer<T>.Default.Equals((T)backingInstance.GetValue(this), value))
			return false;
		
		var attribute = GetType().GetProperty(propertyName)?.GetCustomAttribute<Undoable>();
		if (attribute == null)
		{
			beforeChanged?.Invoke();
			backingInstance.SetValue(this, value);
			onChanged?.Invoke();
			RaisePropertyChanged(propertyName);
		}
		else
		{
			UndoRedoManager.PushAndPerformRecord(undoAction ?? (o =>
			{
				var nowValue = backingInstance.GetValue(this);
				backingInstance.SetValue(this, o);
				RaisePropertyChanged(propertyName);
				return nowValue;
			}), redoAction ?? (o =>
			{
				var nowValue = backingInstance.GetValue(this);
				o ??= value;
				backingInstance.SetValue(this, o);
				RaisePropertyChanged(propertyName);
				return nowValue;
			}), disposeAction);
		}
		
		return true;
	}
}
```
