---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-09-30 23:54:04'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/87977950_p0_master1200.jpg',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-10-04 11:30:03'
parent: 0
password: ''
slug: NET-Core-WPF-打开文件夹对话框
status: publish
tags: [.NET, C#, WPF]
template: ''
title: .NET Core WPF 打开文件夹对话框
type: post
---
# 方法一：曲线救国，用 `OpenFileDialog`

```C#
OpenFileDialog openFileDialog = new OpenFileDialog
{
	Multiselect = false,
	Title = "选择新建项目的位置",
	CheckFileExists = false,
	CheckPathExists = true,
	FileName = "Folder Selection"
};
if (openFileDialog.ShowDialog() == true)
{
	...
}
```

# 方法二：使用 `WinForms` 中的 `FolderBrowserDialog`

更改项目设置：在 `usewpf` 下面增加：

```xml
<UseWindowsForms>true</UseWindowsForms>
```

```C#
using System;
using System.Windows.Forms;

...

using var dialog = new FolderBrowserDialog
{
    Description = "Time to select a folder",
    UseDescriptionForTitle = true,
    SelectedPath = Environment.GetFolderPath(Environment.SpecialFolder.DesktopDirectory)
        + Path.DirectorySeparatorChar,
    ShowNewFolderButton = true
};

if (dialog.ShowDialog() == DialogResult.OK)
{
    ...
}
```

