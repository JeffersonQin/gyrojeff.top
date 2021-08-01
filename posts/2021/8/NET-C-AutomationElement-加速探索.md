---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-01 13:34:29'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-02 00:44:16'
parent: 0
password: ''
slug: NET-C-AutomationElement-加速探索
status: publish
tags: [.NET C#]
template: ''
title: .NET C# AutomationElement 加速探索
type: post
---
前面讲了如何使用`AutomationElement`来获取桌面的`UI`，但是获取速度实在不敢恭维。就拿之前写的那个`DFS`举例，跑一个窗口至少要`1s`，多则`3-5s`，这对于实时控制的需求来说是不可接受的。一开始我以为是我自己的问题，于是乎就做了不少探索。

# 使用`profiler`确定是谁的问题

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801133920.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/c0b3f4fe75c1fb272e38fc230d9a351.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/628a47c1dfa6e140d54485efbdc8509.png)

上图的红框是我主要的实现，可以看到其实占用的时间很低，大部分都是外部调用。所以我们基本上可以确定，这应该是`AutomationElement`实现上面的锅。

# 尝试使用多线程加速`DFS`

为了能够~~更加合理地~~使用多线程，我实现了上一篇文章中的线程池。我先说一下我的线程调度方案：

我们不妨拿这颗树来举例：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801134713.png)

下面标出了递归任务：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801134947.png)

我们原本是按照：

```
0-1 => 1-1 => 1-2 => 0-2 => 2-1 => 2-2 => 2-3 => 0-3 => ...
```

的顺序执行的。那么如果要考虑并行，我们是不是可以这样：

```
p
=> 0-1 + 0-2 + 0-3
=> ...
```

然后子任务也可以参与到线程的抢夺当中。注意：这里我假设同级分发新任务和次级分发新任务相比速度更快，这样可以更好地进行并行。如果速度更慢，那又会变回之前的顺序，反而增加了更多锁机制上的开销。


我们来简单使用一下：

```C#
public static ConcurrentQueue<AutomationElement> GetLeafElementsByParallel(AutomationElement element, int concurrentThread)
{
	ConcurrentQueue<AutomationElement> res = new ConcurrentQueue<AutomationElement>();
	try
	{
		Rect bounds = element.Current.BoundingRectangle;
		if (!JudgeBounding(bounds)) return res;
		IThreadPool<ThreadStart> threadPool = new IThreadPool<ThreadStart>(concurrentThread, new Action<ThreadStart>((threadStart) =>
		{
			threadStart.Invoke();
		}));
		GetLeafElementsDFS(element, res, threadPool, bounds);
		threadPool.Join();
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


private static void GetLeafElementsDFS(AutomationElement element, ConcurrentQueue<AutomationElement> res, IThreadPool<ThreadStart> threadPool, Rect bounds)
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
			if (threadPool.IdleWorkerCount > 0)
			{
				threadPool.EnqueueTask(() =>
				{
					GetLeafElementsDFS(e, res, threadPool, rect);
				});
			} else
			{
				GetLeafElementsDFS(e, res, threadPool, rect);
			}
		}
		catch (Exception)
		{
			continue;
		}
	}
	if (children.Count == 0) res.Enqueue(element);
}
```

但是非常可惜，速度根本提不了多少，只能提个0.3s左右，有时还会更慢。

# 破案了

我一直以为是自己的问题，知道我看到了这篇`stackoverflow`:

https://stackoverflow.com/questions/19631284/my-automationelement-findfirst-findall-seems-to-be-very-slow-how-to-improve-the

> `System.Windows.Automation` is a lorry full of shit. I call `AutomationElement.FindAll(TreeScope.Children, Condition.TrueCondition)` and for returning a ridiculous amount of 30 children Microsoft's crappy code takes incredible 1000 ms on a very fast computer!! Additionally the whole crap is not even threadsafe! It is a shame how `UIAutomation` has been implemented

> The solution to slow performance is to use the new `IUIAutomationElement COM` interface (since Windows 7) instead of the old `System.Windows.Automation` `C#` interface. After that the code will be running LIGHTNING fast! Apart from that the new interface offers much more patterns and Microsoft is extending it continously. In the Windows 10 SDK several patterns and properties have been added.
