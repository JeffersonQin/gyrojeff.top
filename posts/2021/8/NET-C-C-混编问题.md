---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-04 00:23:53'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-04 00:23:53'
parent: 0
password: ''
slug: NET-C-C-混编问题
status: publish
tags: [.NET, C#, C++]
template: ''
title: .NET C# C++ 混编问题
type: post
---
最近一直在做的`Ayase`项目 (获取UI) 性能不太行，于是就开始思考用`C++`。其实纯`C++`我是尝试过的，但是一点 `WinForm` / `MFC` / `C++` 的底子都没有，头大得很。于是乎开始想要使用 `C++` 完成这部分工作，然后再使用`C#` 做 `UI` 等其他内容。

# 解决方案

主要思路：
- `C++` 的项目编译成 `.dll` (动态链接库 `dynamic link library`)
- `C#` 的项目通过 `P/Invoke` 调用 `C++` 暴露出来的接口

# 动手

- **Step 1**: 为**解决方案**添加新**项目**
- **Step 2**: 为新项目添加 `.h` , `.cpp` 文件

`.h` 模板：

```C++
#pragma once
#ifdef GIUIAUTOMATIONMANAGER_EXPORTS
#define GIUIAUTOMATIONMANAGER_API __declspec(dllexport)
#else
#define GIUIAUTOMATIONMANAGER_API __declspec(dllimport)
#endif

extern "C" GIUIAUTOMATIONMANAGER_API int InitializeUIAutomation();

extern "C" GIUIAUTOMATIONMANAGER_API wchar_t* GetForeGroundWindowName();
```

解释：
- 文件名为：`GIUIAutomationManager`
- 定义的一些宏主要是在 `import / export` 方面起作用，大家可以照葫芦画瓢，不用太担心
- `int InitializeUIAutomation()`, `wchar_t* GetForeGroundWindowName()` 是申明的可供外界调用的函数

`.cpp` 模板：

```C++
#include "pch.h" // use stdafx.h in Visual Studio 2017 and earlier
#include "GIUIAutomationManager.h" // 前面的头文件

...
```

这里大家基本上也都能理解了。

- **Step 3**: 配置 `.dll` 的生成路径 和 `.NET` 项目的生成路径

其实大家怎么配都行，之后最后 `DLLImport`的时候的 `.exe` 和 `.dll` 的相对路径关系对就没问题，下面是推荐。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804003848.png)

建议都按照上面这个来：

```
$(SolutionDir)$(Platform)\$(Configuration)\
```

在`.NET`项目中，输出路径粘贴上面这个会自动进行转换，无需担心：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804004018.png)

- **Step 4**: 编译`.dll`: 对`C++`项目右键生成。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804003320.png)

- **Step 5**: `P/Invoke` 调用。

由于`.NET`项目会生成在类似于下面这个地方：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804004231.png)

所以`.dll`相对于`.exe`的目录为: `../<xxx>.dll`

下面是范例：

```C#
[DllImport("../Ayase.Accessibility.dll")]
public static extern int InitializeUIAutomation();

[DllImport("../Ayase.Accessibility.dll", CharSet = CharSet.Auto)]
public static extern IntPtr GetForeGroundWindowName();
```

参数啥的不必在意，看去看API就行。

# 关于**配置**和**平台**

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804003431.png)

善用配置管理器，如果只有`Any CPU`可以选择新建：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804003529.png)

基本上都默认配好了，新平台的话下拉框里选就行。

注意：`.dll` 和 `.NET` 项目必须要同一个架构 (`x64`, `x86`, `ARM64`, ...)

# 注意

如果在`cpp`中用`COM`的接口和对象，需要`import`:

```C++
#include <combaseapi.h>
```

# Reference

- https://docs.microsoft.com/zh-cn/cpp/build/dlls-in-visual-cpp?view=msvc-160
- https://docs.microsoft.com/zh-cn/cpp/build/walkthrough-creating-and-using-a-dynamic-link-library-cpp?view=msvc-160
