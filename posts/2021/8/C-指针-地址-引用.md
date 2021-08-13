---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-04 01:20:05'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-04 01:20:05'
parent: 0
password: ''
slug: C-指针-地址-引用
status: publish
tags: [C++]
template: ''
title: C++ 指针 地址 引用
type: post
---
这两天写`Win32API / C++`，发现自己基本功不行了。下面为自己的理解：

```C++
// 正常定义一个整型 a
int a = 1;

// 对 a 用 & 取地址
cout << "Address of a: " << &a << endl;

// 定义一个整型指针，指向 a
int* b = &a;
// b 则是一个地址
cout << "Address of b: " << b << endl;

// 总结：
// - `*` 取值
// - `&` 取地址
```

注意：**不可以：**

```C++
int *b = 1; // 这样是错误的 int* b 定义的是整型指针类型
```

```C++
#include <iostream>

using namespace std;

int main() {
	int a = 1;
	// 定义引用
	int& b = a;
	cout << "a: " << a << "; b: " << b << endl;
	b = 2;
	cout << "a: " << a << "; b: " << b << endl;
	a = 4;
	cout << "a: " << a << "; b: " << b << endl;
	// 可以理解为 "一模一样的分身" , 一个变了，另一个也变
	return 0;
}

/* Output:
a: 1; b: 1
a: 2; b: 2
a: 4; b: 4
*/
```

下面摘自：https://www.cnblogs.com/yanlingyin/archive/2011/12/07/2278961.html

```C++
#include<iostream>

using namespace std;

//值传递
void change1(int n) {
	cout << "值传递--函数操作地址" << &n << endl;         //显示的是拷贝的地址而不是源地址 
	n ++;
}

//引用传递
void change2(int & n) {
	cout << "引用传递--函数操作地址" << &n << endl; 
	n ++;
}

//指针传递
void change3(int *n) {
	cout << "指针传递--函数操作地址" << n << endl; 
	*n = *n + 1;
}

int main() {
	int n = 10;
	cout << "实参的地址" << &n << endl;
	change1(n);
	cout << "after change1() n=" << n << endl;
	change2(n);
	cout << "after change2() n=" << n << endl;
	change3(&n);
	cout << "after change3() n=" << n << endl;
	return true;
}
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210804014814.png)

注意：
- 传指针给一个函数，只有改变 `*ptr` 的内容才能真正改变这个变量的值。如果直接修改 `ptr` 为别的地址，原来那个变量无论是值还是地址，都没有改变，因为只修改了一个 **地址变量**
- 关于 `**` : 指向指针的指针。
