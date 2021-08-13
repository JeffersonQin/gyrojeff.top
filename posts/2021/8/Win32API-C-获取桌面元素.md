---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-06 00:13:44'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-06 00:13:44'
parent: 0
password: ''
slug: Win32API-C-获取桌面元素
status: publish
tags: [C++, Win32API]
template: ''
title: Win32API C++ 获取桌面元素
type: post
---
# 关于赋值问题

- 一律用指针（因为大家都这么干）
- 传入一个指针（即要赋值的地址），记作`xxx* a`，赋值的时候只需要`*a = xxx`即可

# 关于返回值问题

- 都是用`HRESULT`，这个是`COM`接口的主要实现方法。根据文档：`HRESULT`类型大于等于零即为操作成功（包括返回“FALSE”，因为操作成功了，所以就是成功），小于零的即是操作失败。

# `C#`中`P/Invoke`的实现问题

- 需要搞清楚`int`, `long`, `uint`, `ulong`, 等类型的长度，使得`C++`与`C#`的`extern`函数格式是一样的。
- 赋值的指针可以使用`out IntPtr`。
- 从`C#`传整型的指针可以用`ref int`。

# 关于Variant用法

- 需要先声明变量（不能声明指针），然后通过`VariantInit`方法进行初始化，最后再通过`VariantClear`方法销毁：
	```C++
	VARIANT varProcessId;
	VariantInit(&varProcessId);

	// ...

	VariantClear(&varProcessId);
	```
- 存在两个重要的字段：`vt` (类型), `***Val` (相应类型的值)。详情可以看文档，这里给出一个例子：
	```C++
	varProcessId.vt = VT_I4;
    varProcessId.uintVal = 4579;
	```

# 关于向`C#`传递自定义数据类型

传递自定义的`class`或者`struct`建议全部`pass by reference`，当然了，`pass by value`也不是不可以，就是实现起来更加麻烦罢了。如果`pass by reference`的话需要传递指针。在`C++`端可以直接正常写，比方说：

```C++
extern xxx* method_A(...)
```

而在`C#`这里，则直接写`IntPtr`即可：

```C#
extern IntPtr method_A(...)
```

同样，如果是赋值类的方法，可能会出现双指针，比如:

```C++
extern int method_B(xxx** result)
```

那么我们就可以通过`out IntPtr`来简化双指针：

```C#
extern int method_B(out IntPtr result)
```

# 关于`new`, `delete`

`new`关键词开辟内存空间，变量离开作用域时不会被自动销毁，从`C++`向`C#`传递指针时我就是用的这个特性。使用：

```C++
Object_A *obj = new Object_A();
```

注意：像下面这样的申明没有开辟内存空间, 就会出现空指针：

```C++
Object_A *obj;
```

注意：像下面这样申明变量，变量离开作用域就会被销毁：

```C++
Object_A obj;
```

`delete`关键词就是回收内存空间，防止内存泄漏。使用范例：

```C++
delete obj;
```

所以从`C++`向`C#`传递值我的方法：
1. 使用`new`关键词在`C++`开辟内存空间
2. 将这个变量的指针传给`C#`
3. 在`C#`中运用，或者传给`C++`再返回其他想要的东西，总之就是一番使用
4. 使用完毕后，将这个指针传给`C++`，用`delete`关键词销毁。

# 关于`C++`中`struct`和`class`的区别

Stackoverflow上告诉我，没有区别，完全是使用习惯问题。他们全都是`pass by value`.

# 关于向`C#`传递`std::vector`

和上面说的一样，直接传递这个`vector`的指针即可。

# 关于字符串`BSTR`的复制

- 一开始返回名称在某些时候是失败的，因为是从`VARIANT`里拿过来的，`ClearVariant`了以后字符串首指针被回收，剩下的内容不知道什么时候会被回收，不稳定，会拿到大批`Local\`这样的值。
- 最终的解决办法：复制一份：
  ```C++
  // varGet是VARIANT类型
  *name = _bstr_t(varGet.bstrVal, false).copy(true);
  ```

生命周期问题：SO上有回答。这个不需要我们关心，系统帮我们解决。

# 关于`COM`在`x64`下的报错

自己实验的时候，发现`COM`下`x86`比`x64`更快，并且还没有奇怪的报错。后来这个项目在v2ex上发帖之后，有网友解答了疑问：

> 顺带一提，除了 System.Windows.Automation 和 COM，微软最近还在 winrt API 下也加入了一套 UI Automation 相关 API （虽然猜测底层和 COM 一样）
>
> https://docs.microsoft.com/en-us/uwp/api/windows.ui.uiautomation.automationelement?view=winrt-20348
>
> UI Automation 很多年没更新了，它的 IPC 架构导致性能非常差，比如 64 位比 32 位慢，猜测就是 IPC 层要做很多转换导致。微软最近也在做一些新的尝试：
>
> https://news.ycombinator.com/item?id=24294070

# 关于最终速度

显著提高！不过不在`UI`线程、主线程时速度会有所降低，还在探索中。
