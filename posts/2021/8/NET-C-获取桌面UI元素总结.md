---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-02 13:12:53'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-02 13:26:29'
parent: 0
password: ''
slug: NET-C-获取桌面UI元素总结
status: publish
tags: [.NET, C#]
template: ''
title: .NET C# 获取桌面UI元素总结
type: post
---
# 结论

前几篇文章在这里：
1. [.NET C# AutomationElement 获取桌面UI元素](/index.php/archives/NET-C-AutomationElement-%E8%8E%B7%E5%8F%96%E6%A1%8C%E9%9D%A2UI%E5%85%83%E7%B4%A0/)
2. [.NET C# 手撕线程池 | 县城撕裂者](/index.php/archives/NET-C-%E6%89%8B%E6%92%95%E7%BA%BF%E7%A8%8B%E6%B1%A0-%E5%8E%BF%E5%9F%8E%E6%92%95%E8%A3%82%E8%80%85/)
3. [.NET C# AutomationElement 加速探索](/index.php/archives/NET-C-AutomationElement-%E5%8A%A0%E9%80%9F%E6%8E%A2%E7%B4%A2/)
4. [.NET C# UIAutomationElement 获取桌面元素](/index.php/archives/NET-C-UIAutomationElement-%E8%8E%B7%E5%8F%96%E6%A1%8C%E9%9D%A2%E5%85%83%E7%B4%A0/)
5. [.NET C# 通过Win32API获取窗口及控件的信息](/index.php/archives/NET-C-%E9%80%9A%E8%BF%87Win32API%E8%8E%B7%E5%8F%96%E7%AA%97%E5%8F%A3%E5%8F%8A%E6%8E%A7%E4%BB%B6%E7%9A%84%E4%BF%A1%E6%81%AF/)

经过几天的研究初步获得了如下的结论：
1. `Win32API`不靠谱
2. 和`Snipaste`开发大佬交流后得知的MSAA方法有局限，对于`Text`属性可能不能读取（这个功能对我来说至关重要，替代品为`UIAutomation`）

先说测试后的结论：
1. 竟然是`System.Windows.Automation`最高效
2. 手写的剪枝`DFS`比微软封装的方法高效
3. 如果测试的时候用户在操作其他UI或有UI变化会导致速度变慢
4. 多线程优化几乎没有效果

猜测：
1. 瓶颈就是这个需求本身
2. 速度取决于Server端，和如何实现无关

# 测试

测试范围：
- `System.Windows.Automation`
  - 手写带有剪枝的`DFS`
  - 线程池优化的剪枝`DFS`
  - 使用`Walker`写`DFS`
  - 直接使用`FindAll(TreeScope.Descendants)`
- `UIAutomationClient (COM)`
  - 手写带有剪枝的`DFS`
  - 线程池优化的剪枝`DFS`
  - 使用`Walker`写`DFS`
  - 直接使用`FindAll(TreeScope.Descendants)`

测试代码：

```C#
// IUIAutomation
IUIAutomationElement element = GIUIAutomationManager.GetWindowByProcessID(15204);
{
	StreamWriter file = new StreamWriter("iui.out", append: true);
	for (int j = 0; j < 100; j++)
	{
		DateTime ts_start = DateTime.UtcNow;
		List<IUIAutomationElement> res = GIUIAutomationManager.GetLeafElementsDFS(element);
		DateTime ts_end = DateTime.UtcNow;
		Debug.WriteLine("iui, epoch: " + (j + 1));
		Debug.WriteLine((ts_end - ts_start).TotalSeconds.ToString() + " " + res.Count);
		await file.WriteLineAsync((ts_end - ts_start).TotalSeconds.ToString() + " " + res.Count);
	}
}
for (int i = 1; i <= 24; i ++)
{
	StreamWriter file = new StreamWriter("iui_multi_" + i + ".out", append: true);
	GThreadPool<ThreadStart> threadPool = new GThreadPool<ThreadStart>(i, new Action<ThreadStart>((threadStart) =>
	{
		threadStart.Invoke();
	}));
	for (int j = 0; j < 100; j++)
	{
		DateTime ts_start = DateTime.UtcNow;
		ConcurrentQueue<IUIAutomationElement> res = GIUIAutomationManager.GetLeafElementsByParallel(element, threadPool);
		DateTime ts_end = DateTime.UtcNow;
		Debug.WriteLine("iui, thread: " + i + ", epoch: " + (j + 1));
		Debug.WriteLine((ts_end - ts_start).TotalSeconds.ToString() + " " + res.Count);
		await file.WriteLineAsync((ts_end - ts_start).TotalSeconds.ToString() + " " + res.Count);
	}
}
// AutomationElement
AutomationElement a_element = GAutomationManager.GetWindowByProcessID(15204);
{
	StreamWriter file = new StreamWriter("ae.out", append: true);
	for (int j = 0; j < 100; j++)
	{
		DateTime ts_start = DateTime.UtcNow;
		List<AutomationElement> res = GAutomationManager.GetLeafElements(a_element);
		DateTime ts_end = DateTime.UtcNow;
		Debug.WriteLine("ae, epoch: " + (j + 1));
		Debug.WriteLine((ts_end - ts_start).TotalSeconds.ToString() + " " + res.Count);
		await file.WriteLineAsync((ts_end - ts_start).TotalSeconds.ToString() + " " + res.Count);
	}
}
for (int i = 1; i <= 24; i++)
{
	StreamWriter file = new StreamWriter("ae_multi_" + i + ".out", append: true);
	GThreadPool<ThreadStart> threadPool = new GThreadPool<ThreadStart>(i, new Action<ThreadStart>((threadStart) =>
	{
		threadStart.Invoke();
	}));
	for (int j = 0; j < 100; j++)
	{
		DateTime ts_start = DateTime.UtcNow;
		ConcurrentQueue<AutomationElement> res = GAutomationManager.GetLeafElementsByParallel(a_element, threadPool);
		DateTime ts_end = DateTime.UtcNow;
		Debug.WriteLine("ae, thread: " + i + ", epoch: " + (j + 1));
		Debug.WriteLine((ts_end - ts_start).TotalSeconds.ToString() + " " + res.Count);
		await file.WriteLineAsync((ts_end - ts_start).TotalSeconds.ToString() + " " + res.Count);
	}
}
MessageBox.Show("Done!");
```
