---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2020-09-10 13:07:42'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2020-09-10 13:07:42'
parent: 0
password: ''
slug: WPF实现系统托盘
status: publish
tags: [.NET, C#, WPF]
template: ''
title: WPF实现系统托盘
type: post
---
在WinForm当中，我们可以使用非常轻松地实现系统托盘，在.NET Core的WPF当中，`System.Windows.Form`并不能作为命名空间引用，本文将讲述如何在WPF中实现系统托盘，以及如何将窗口最小化到系统托盘。

# 安装NuGet包

搜索`Hardcodet.NotifyIcon.Wpf`并安装：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801105724.png)

# 编写ViewModel

```C#
public class NotifyIconViewModel
{
    /// <summary>
    /// 激活窗口
    /// </summary>
    public ICommand ShowWindowCommand
    {
        get
        {
            return new DelegateCommand
            {
                CommandAction = () =>
                {
                    Application.Current.MainWindow.Activate();
                }
            };
        }
    }

    /// <summary>
    /// 隐藏窗口
    /// </summary>
    public ICommand HideWindowCommand
    {
        get
        {
            return new DelegateCommand
            {
                CommandAction = () => Application.Current.MainWindow.Hide()
            };
        }
    }


    /// <summary>
    /// 关闭软件
    /// </summary>
    public ICommand ExitApplicationCommand
    {
        get
        {
            return new DelegateCommand { CommandAction = () => Application.Current.Shutdown() };
        }
    }
}
```

其中，虽然代码中用到了`DelegateCommand`类，但实际上并未实际发生作用，只是为了更好地封装代码，增加某些特定条件下的可读性，读者也可自行将其更改为普通的Command。下面为`DelegateCommand`类代码：

```C#
public class DelegateCommand : ICommand
{
    public Action CommandAction { get; set; }
    public Func<bool> CanExecuteFunc { get; set; }

    public void Execute(object parameter)
    {
        CommandAction();
    }

    public bool CanExecute(object parameter)
    {
        return CanExecuteFunc == null || CanExecuteFunc();
    }

    public event EventHandler CanExecuteChanged
    {
        add { CommandManager.RequerySuggested += value; }
        remove { CommandManager.RequerySuggested -= value; }
    }
}
```

# 在App.xaml中添加托盘信息并关联ViewModel

在App.xaml添加命名空间：

```XAML
xmlns:tb="http://www.hardcodet.net/taskbar"
```

如果策略并不是隐藏窗口而是直接关闭窗口，那么为了防止所有窗口都退出导致的程序关闭，我们需要添加额外的一行属性：

```XAML
ShutdownMode="OnExplicitShutdown"
```

而不是原来的`OnLastWindowClose`。接下来，我们在`Application.Resource`标签内添加系统托盘：

```XAML
<Application.Resources>
    <ContextMenu x:Shared="false" x:Key="SysTrayMenu">
        <MenuItem Header="显示窗口" Command="{Binding ShowWindowCommand}" />
        <MenuItem Header="关闭窗口" Command="{Binding HideWindowCommand}" />
        <Separator />
        <MenuItem Header="退出" Command="{Binding ExitApplicationCommand}" />
    </ContextMenu>
    <tb:TaskbarIcon x:Key="Taskbar"
                    ToolTipText="UIToy by Haoyun Qin"
                    DoubleClickCommand="{Binding ShowWindowCommand}"
                    ContextMenu="{StaticResource SysTrayMenu}"
                    IconSource="UIToy.ico">
        <tb:TaskbarIcon.DataContext>
            <local:NotifyIconViewModel />
        </tb:TaskbarIcon.DataContext>
    </tb:TaskbarIcon>
</Application.Resources>
```

上面，我们针对不同的操作，绑定了相关的命令。还需要添加一个`ico`文件（这里我添加的是`UIToy.ico`）

图标文件要设置好生成属性：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220103195723.png)

# 在启动时显示系统托盘

在`App.xaml.cs`当中初始化`Taskbar`：

```C#
public partial class App : Application
{
    public static TaskbarIcon TaskbarIcon;

    protected override void OnStartup(StartupEventArgs e)
    {
        base.OnStartup(e);
        TaskbarIcon = (TaskbarIcon)FindResource("Taskbar");

    }
}
```

然后我们就可以发现，图标已经在系统托盘当中显示了。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/2.jpg)
