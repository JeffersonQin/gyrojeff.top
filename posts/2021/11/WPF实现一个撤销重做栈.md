---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-11-22 12:54:00'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbChoice: default, thumbDesc: '', thumbSmall: '',
  thumbStyle: default}
modified: '2021-11-29 21:56:12'
parent: 0
password: ''
slug: wpf-implement-undo-redo
status: publish
tags: [.NET, C#, WPF, 栈]
template: ''
title: WPF实现一个撤销重做栈
type: post
---
最近在做编辑器相关的项目，看起来都很好，但当我用的时候，下意识按了 `Ctrl + Z` 想要撤销，这才发现撤销重做完全没做。

# 撤销重做的几种方式

## Deep Copy

这是最简单也是最暴力的一种办法，即，比方说我们在操作一个文件的数据结构，我们每对它进行一步操作，就 Deep copy 整个对象 (即包括内部的 List, Array, Map 等)。维护一个栈，回退就是 `head_pointer` 减一，重做就是 `head_pointer` 加一 (如果可以的话)。

优点:
* 写起来简单又暴力
* 不用进行大规模的重构

缺点:
* 需要在所以数据改变的地方都进行修改，费力
* 耗时、耗内存
* 无法解决循环引用的问题，即：
  ```
  A.object_B = B;
  B.object_A = A;
  ```

## Command Pattern 命令模式

这是设计模式的一种，我们需要将所有操作数据的代码全部改写。定义一个 `Action` 类，所有的操作全部继承这个类，并实现 `Perform`，`Undo`，`Redo`

有人会说，这样无法进行一些不可逆操作的编写，比方说高斯模糊，但是其实也是有解决办法的，比方说在执行 Action 之前便复制一份对象，在 `Undo` 时取回来即可。

Command Pattern 无疑是实现撤消重做的最好的方法，因为我们只需要实现一个 Manager 类进行管理，然后对于每个 Action 实现相应的子类即可。

# Command Pattern 带来的问题

不可置否，Command Pattern 非常好，但是，对于已经写了几千行代码的项目很不友好。新的设计模式代表所有东西全部需要进行重构，需要把原来写在一个 `Class` 里的东西全部抽象成 `Action` 类，工作量惊人。

此外，对于 `MVVM` 项目，特别是使用了双向数据绑定时，我们甚至需要修改 UI 代码，把原先的 `Data Binding` 替换为 `Command`，否则是无法由 `Manager` 进行管理的。

# 折中的方案？

综上所述，我的诉求便是不需要抽象成 `Action`，同时又不需要把 `Data Binding` 进行重构的方案，这个时候，下面这套黑魔法应运而生！

* `Lambda`
* 直接在 `setter` 里写 `UndoRedo`

看！这样的黑魔法可以完美满足上面的要求，只要你敢想，什么都能实现！

# 操作数据结构的设计

根据上面所说，我们需要设计一种数据结构来记录操作，其应该具有四个必要的成分：

* 存放临时数据的对象
* `Undo` 行为 (`delegate function`)
* `Redo` 行为 (`delegate function`)
* `Perform` 行为 (`delegate function`)

然而，在实践的过程中，我们不难发现 `Redo` 和 `Perform` 在很多情况下都有很大的重叠，所以我们其实可以合并起来，而区分是否是 `Redo` 还是 `Perform` 只需要 `delegate` (`lambda`) 传入的参数是否是 `null` 即可，这里还可以使用 C# 的语法糖 `??=` 来进行判断 + 赋值。这些技巧后文都会分别讨论。

此外，由于我的业务层还涉及到了需要手动 `Dispose` 的 `object` (`OpenCVSharp` 的 `Mat` 和 `UMat`)，所以我还需要一个 `Dispose` 的 `delegate function`。

下面是数据结构的设计：

```C#
public class UndoRedoRecord
{
	public object Value;

	public delegate void DelegateActionWithValue(object value);
	
	public delegate object DelegateActionWithAndReturnValue(object value);

	public DelegateActionWithValue DisposeAction;

	public DelegateActionWithAndReturnValue UndoAction, RedoAction;

	public UndoRedoRecord(
		DelegateActionWithAndReturnValue undoAction, 
		DelegateActionWithAndReturnValue redoAction, 
		DelegateActionWithValue disposeAction = null)
	{
		UndoAction = undoAction;
		RedoAction = redoAction;
		DisposeAction = disposeAction;
	}
}
```

# 撤回重做栈的设计

考虑到我们可能会需要同时撤回多个操作，同时重做多个操作，这里使用双层的 `List` 来实现。

```C#
List<List<UndoRedoRecord>> UndoStack
```

此外，我们需要管理：

* 当前处在栈的什么位置
* 栈最远什么地方是可达的（如果在撤销后有新的操作覆盖那么更远的元素就不可达了）

所以我们引入了 `head` 和 `max` 两个指针。

# Manager 的操作讲解

注意：本节不考虑双层 `List`，那个只是代码抽象的问题，工程上动点手脚就能实现，这里主要讲解流程问题。

## 入栈（以修改数据为例）

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211123132130.png)

图中 `val_A` ~ `val_G` 是 7 个不同的属性，我们想让他们支持撤消重做，蓝色的 `step` 标出了操作的顺序，我们需要在执行操作前就把当前状态 (和操作的 `lambda`) 记录下来，入栈。

## 数据流动（以修改数据为例）

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211123134226.png)

## 修改数据记录的 lambda demo

上上一节讲了入栈是数据的记录，上一节讲了数据流动，但是都和我们定义的 `UndoAction`, `RedoAction` 无关，本节给出一种具体实现。

普通的属性：

```C#
private string _text;

public string Text {
	get => _text;
	set => UndoRedoManager.PushAndPerformRecord(o =>
	{
		// Undo 操作：把之前存好的数据和现在的数据互相替换
		var nowValue = Text;
		_text = o;
		return nowValue;
	}, o =>
	{
		// Redo 操作：把之前存好的数据和现在的数据互相替换 (其实这个例子和 Undo 差不多)
		var nowValue = Text;
		o ??= value; // 检查 o 是否是 null，若是则代表是第一次执行，赋值为输入的 value
		_text = o;
		return nowValue;
	});
}
```

如果是 `Dependency Object` (`SetProperty` 怎么写这里就不给了，具体可以去看微软的文档):

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

## 撤销后数据覆盖

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211123135856.png)

将 `head` 到 `max` 的 `record` 全部进行 `Dispose`，然后将指针都设置为入栈位置。

## 清空

几个指针全部归零 (-1)，然后对于所有还在栈里的元素进行 `Dispose`，我这里共用了 `Stack`，如果想要进行垃圾回收的话可以新建 `List` 对象。

# 让 Manager 支持多个记录同时撤销重做

说难其实不难，但是有个坑点：

* `Redo` 是正序、`Undo` 是反序

# Manager 的一些 Flag

* `IgnoreOtherRecording`
  * 在进行 `Undo` 和 `Redo` 的过程中，不应该产生其他的记录，否则会引起栈的混乱，所以需要打开这个 Flag
  * 用户如果想要进行一些刷新操作可以手动开启这个 Flag，注意开启规范：必须要先暂存开启前的值，结束后再回复，这是个坑。即：
    ```C#
	var lastIgnoreStatus = IgnoreOtherRecording;
	IgnoreOtherRecording = true;
	// ...
	IgnoreOtherRecording = lastIgnoreStatus;
	```
* `ContinuousRecording`：开启此 Flag 后之后知道再次关闭前的操作都会被并入一组，在以后同时 Undo 或 Redo。这是工程问题，不多讨论。

# Manager 实现

```C#
public static class UndoRedoManager
{
    private static int _head = -1;
    private static int _max = -1;
    
    public static bool IgnoreOtherRecording = false;
    
    public static bool ContinuousRecording = false;
    
    public static List<List<UndoRedoRecord>> UndoStack = new List<List<UndoRedoRecord>>();
    public static void StartRecording()
    {
        IgnoreOtherRecording = false;
    }
    public static void StopRecording()
    {
        IgnoreOtherRecording = true;
    }
    
    public static void PushRecord(List<UndoRedoRecord> records)
    {
        if (IgnoreOtherRecording) return;
        var tempHead = _head;
        while (tempHead < _max)
        {
            tempHead ++;
            foreach (var redoRecord in UndoStack[tempHead])
                redoRecord.DisposeAction?.Invoke(redoRecord.Value);
        }
        _max = ++ _head;
        if (_head >= UndoStack.Count)
            UndoStack.Add(records);
        else UndoStack[_head] = records;
    }
    public static void PushAndPerformRecord(List<UndoRedoRecord> records)
    {
        PushRecord(records);
        var lastIgnoreStatus = IgnoreOtherRecording;
        IgnoreOtherRecording = true;
        foreach (var record in records)
            record.Value = record.RedoAction(null);
        IgnoreOtherRecording = lastIgnoreStatus;
    }
    public static void PushRecord(UndoRedoRecord record)
    {
        if (IgnoreOtherRecording) return;
        if (ContinuousRecording)
            UndoStack[_head].Add(record);
        else PushRecord(new List<UndoRedoRecord> { record });
    }
    public static void PushAndPerformRecord(UndoRedoRecord record)
    {
        PushRecord(record);
        var lastIgnoreStatus = IgnoreOtherRecording;
        IgnoreOtherRecording = true;
        record.Value = record.RedoAction(null);
        IgnoreOtherRecording = lastIgnoreStatus;
    }
    public static void PushRecord(
        UndoRedoRecord.DelegateActionWithAndReturnValue undoAction, 
        UndoRedoRecord.DelegateActionWithAndReturnValue redoAction, 
        UndoRedoRecord.DelegateActionWithValue disposeAction = null)
    {
        PushRecord(new UndoRedoRecord(undoAction, redoAction, disposeAction));
    }
    public static void PushAndPerformRecord(
        UndoRedoRecord.DelegateActionWithAndReturnValue undoAction, 
        UndoRedoRecord.DelegateActionWithAndReturnValue redoAction, 
        UndoRedoRecord.DelegateActionWithValue disposeAction = null)
    {
        PushAndPerformRecord(new UndoRedoRecord(undoAction, redoAction, disposeAction));
    }
    
    public static void StartContinuousRecording()
    {
        ContinuousRecording = true;
        PushRecord(new List<UndoRedoRecord>());
    }
    public static void StopContinuousRecording()
    {
        ContinuousRecording = false;
    }
    public static void Undo()
    {
        if (_head < 0) return;
        var lastIgnoreStatus = IgnoreOtherRecording;
        IgnoreOtherRecording = true;
        for (var i = UndoStack[_head].Count - 1; i >= 0; i --)
            UndoStack[_head][i].Value = UndoStack[_head][i].UndoAction?.Invoke(UndoStack[_head][i].Value);
        _head --;
        IgnoreOtherRecording = lastIgnoreStatus;
    }
    public static void Redo()
    {
        if (_head == _max) return;
        var lastIgnoreStatus = IgnoreOtherRecording;
        IgnoreOtherRecording = true;
        _head ++;
        foreach (var record in UndoStack[_head])
            record.Value = record.RedoAction?.Invoke(record.Value);
        IgnoreOtherRecording = lastIgnoreStatus;
    }
    public static void Clear()
    {
        if (IgnoreOtherRecording) return;
        for (var i = 0; i < _max; i ++)
            foreach (var record in UndoStack[i])
                record.DisposeAction?.Invoke(record.Value);
        _head = _max = -1;
    }
}
```

# 有多个值需要被同时记录的情形

注意：这里讨论的问题和前面所说的多条记录一起撤消重做不同，而是一条记录需要保存多个数据。之所以会产生这样的问题，是因为我们的数据结构设计的只有一个 `Value` 对象。解决这个问题非常简单，有多种方式，比方说自定义一个 `class` / `struct`，甚至可以定义一个 `List<object>`，把东西暴力塞进去，下面是一个例子:

```C#
UndoRedoManager.PushAndPerformRecord(o =>
{
	var removedItem = ((List<object>)o)[0] as YuzuNotationGroup;
	var removedIndex = ((List<object>)o)[1] as int? ?? 0;
	removedItem?.Load();
	NotationGroups.Insert(removedIndex, removedItem);
	RefreshImageList();
	return o;
}, o =>
{
	o ??= new List<object>
	{
		SelectedNotationGroupItem,
		NotationGroups.IndexOf(SelectedNotationGroupItem)
	};
	var removedItem = ((List<object>)o)[0] as YuzuNotationGroup;
	removedItem?.Unload();
	NotationGroups.Remove(removedItem);
	RefreshImageList();
	return o;
});
```

# 撤销重做产生中间值的情形

例子：

```C#
UndoRedoManager.PushAndPerformRecord(message =>
{
	message = Manager.YuzuMarkerManager.PopMessage(ViewModel);
	return message;
}, message =>
{
	message ??= "按住 Ctrl + 鼠标左键选择工作区域，点击画布外侧区域取消，Enter键确认区域";
	Manager.YuzuMarkerManager.PushMessage(ViewModel, message as string);
	return message;
});
```

# 撤销重做和值无关的情形

下面是一个撤销重做 List 内元素顺序的例子：

```C#
UndoRedoManager.PushAndPerformRecord(o =>
{
	NotationGroups.Move(index - 1, index);
	RefreshImageList();
	return null;
}, o =>
{
	NotationGroups.Move(index, index - 1);
	RefreshImageList();
	return null;
});
```
