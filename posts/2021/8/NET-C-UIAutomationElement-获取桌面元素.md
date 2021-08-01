---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-01 20:14:50'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801203038.png',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-01 20:14:50'
parent: 0
password: ''
slug: NET-C-UIAutomationElement-获取桌面元素
status: publish
tags: [.NET, C#]
template: ''
title: .NET C# UIAutomationElement 获取桌面元素
type: post
---
继上一篇文章所说，我开始尝试使用`UIAutomationElement`，试试看能否提高效率。由于`UIAutomationElement`是`COM`组件，所以我们需要配置项目引用：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801201936.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801202010.png)

# 文档索引

下面的几篇文档有这个库的详细解释：
- 接口总览：https://docs.microsoft.com/zh-cn/windows/win32/api/uiautomationclient/
- 一些Reference: https://docs.microsoft.com/en-us/windows/win32/winauto/entry-uiauto-win32
- PropertyID (包含在上一条): https://docs.microsoft.com/en-us/windows/win32/winauto/uiauto-automation-element-propids
- CUIAutomation (必须通过这个接口才能访问): https://docs.microsoft.com/en-us/previous-versions/windows/desktop/legacy/ff384838(v=vs.85)

顺便找到了一个有趣的封装：https://github.com/TestStack/UIAComWrapper/

# 代码

下面我贴出使用`IUIAutomation`的代码，具体功能可以对照`AutomationElement`:

```C#
class IUIAutomationManager
{
	public static CUIAutomation automation = new CUIAutomation();
	private static IUIAutomationCondition ConditionNotOffScreen = 
		automation.CreatePropertyCondition(UIA_PropertyIds.UIA_IsOffscreenPropertyId, false);

	public static IUIAutomationElement GetDesktop()
	{
		return automation.GetRootElement();
	}


	public static IUIAutomationElement GetWindowByProcessID(int processID)
	{
		return GetDesktop().FindFirst(TreeScope.TreeScope_Children,
			automation.CreatePropertyCondition(UIA_PropertyIds.UIA_ProcessIdPropertyId, processID));
	}


	public static List<IUIAutomationElement> GetLeafElements(IUIAutomationElement element)
	{
		List<IUIAutomationElement> res = new List<IUIAutomationElement>();
		try
		{
			Rect bounds = GetBoundingRectangle(element);
			if (!JudgeBounding(bounds)) return res;
			GetLeafElementsDFS(element, res, bounds);
		}
		catch (Exception)
		{
			return res;
		}
		return res;
	}

	public static ConcurrentQueue<IUIAutomationElement> GetLeafElementsByParallel(IUIAutomationElement element, int concurrentThread)
	{
		ConcurrentQueue<IUIAutomationElement> res = new ConcurrentQueue<IUIAutomationElement>();
		try
		{
			Rect bounds = GetBoundingRectangle(element);
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


	public static Rect GetBoundingRectangle(IUIAutomationElement element)
	{
		double[] bounds = (double[])element.GetCurrentPropertyValue(UIA_PropertyIds.UIA_BoundingRectanglePropertyId);
		if (bounds == null) { return new Rect(0, 0, 0, 0); }
		return new Rect(bounds[0], bounds[1], bounds[2], bounds[3]);
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


	private static void GetLeafElementsDFS(IUIAutomationElement element, List<IUIAutomationElement> res, Rect bounds)
	{
		IUIAutomationElementArray children = element.FindAll(TreeScope.TreeScope_Children, ConditionNotOffScreen);
		for (int i = 0; i < children.Length; i ++)
		{
			IUIAutomationElement e = children.GetElement(i);
			try
			{
				Rect rect = GetBoundingRectangle(e);
				if (!JudgeBounding(rect)) continue;
				if (rect.Bottom <= bounds.Top || rect.Right <= bounds.Left) continue;
				if (rect.Top >= bounds.Bottom || rect.Left >= bounds.Right) continue;
				GetLeafElementsDFS(e, res, rect);
			}
			catch (Exception)
			{
				continue;
			}
		}
		if (children.Length == 0) res.Add(element);
	}

	private static void GetLeafElementsDFS(IUIAutomationElement element, ConcurrentQueue<IUIAutomationElement> res, IThreadPool<ThreadStart> threadPool, Rect bounds)
	{
		IUIAutomationElementArray children = element.FindAll(TreeScope.TreeScope_Children, ConditionNotOffScreen);
		for (int i = 0; i < children.Length; i ++)
		{
			IUIAutomationElement e = children.GetElement(i);
			try
			{
				Rect rect = GetBoundingRectangle(e);
				if (!JudgeBounding(rect)) continue;
				if (rect.Bottom <= bounds.Top || rect.Right <= bounds.Left) continue;
				if (rect.Top >= bounds.Bottom || rect.Left >= bounds.Right) continue;
				if (threadPool.IdleWorkerCount > 0)
				{
					threadPool.EnqueueTask(() =>
					{
						GetLeafElementsDFS(e, res, threadPool, rect);
					});
				}
				else
				{
					GetLeafElementsDFS(e, res, threadPool, rect);
				}
			}
			catch (Exception)
			{
				continue;
			}
		}
		if (children.Length == 0) res.Enqueue(element);
	}
}
```

后来也使用了线程池等手段优化，但是效果仍然不尽如人意。下面是性能分析：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801202859.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801203038.png)

# 总结与反思

我仔细对比了这个方法和`AutomationElement`的办法，他们的耗时其实是差不多的。而且极其相似的`API`不由得让我思考很有可能`AutomationElement`就是`IUIAutomation`的封装（大概），所以我们还需要另辟蹊径。

可靠大先辈提到了窗体/控件句柄，我认为这个方法非常值得尝试。马上可以再来实践一下。

但是这仍然解答不了我关于“明明是系统实现为什么还是这么低效的疑惑”，于是乎便上`stackoverflow`进行提问。~~（没过多久真的有人来回答了！）~~，链接如下：https://stackoverflow.com/questions/68609370/how-to-get-ui-tree-in-c-sharp-with-high-performance

> This is clearly not why UI Automation exists. It provides you a proxy for a given application "UI element" (whatever what that really is), not a general UI tree that you can completely browse. In general, you never want to browse the whole tree, but find discriminant properties that will get you the most directly possible to an element (using id, control type, class name, pattern support, etc.). Browsing the tree can lead to code hanging. This is all by design, not a bug. You have no idea what applications need to unveil their elements to an end user. For example, it can pop something up.

> 我: Thanks! Your advice really helps, and I am now trying to get the elements through win32apis. Well, in this case, I really want to browse the whole UI tree of a window, because I'm now developing a tool aimed to control everything by keyboard. If you are interested in it, the repo is here: github.com/JeffersonQin/Ayase

> Note UI Automation *is* the right tool, you won't do anything better with raw Win32 api. It's just a limitation of driving other application using their UIs.

> 我：Well, I some how doubt this conclusion. Because, you know, there certainly have some ways to walk through UI Tree very quickly. The snipping tool `snipaste` is a very good example, when mouse moves, it can automatically sticks to UI elements, and I'm curious about how was it implemented. snipaste: snipaste.com

> Getting the element from the mouse is easy: [docs.microsoft.com/en-us/dotnet/api/…](https://docs.microsoft.com/en-us/dotnet/api/system.windows.automation.automationelement.frompoint) and walking ascendency is fast too. 

所以总结一下，他认为`UIAutomation`就是正确的工具。好吧，先尝试一下`Win32API`到底怎么样再来考虑，毕竟实践出真知嘛。

# 看文档时的一些疑惑

- 为什么要`UIAutomationElement 1/2/3/4/5/6/7/8/9`?
- `MIT`大佬：
  > 这很合理啊。因为backward compatibility。这本质上和Windows的一堆Ex函数没区别，就是甲方无限加需求。直接在源代码上改，一方面可能会导致逻辑向后不兼容，如果逻辑向后兼容，加代码可能会改变内存布局导致API不兼容，所以就开新的出来继承之前的。Automation看起来是一个不断需求增多的领域，所以这种修改发生了四五次（，甚至是9次。我真的服了。
