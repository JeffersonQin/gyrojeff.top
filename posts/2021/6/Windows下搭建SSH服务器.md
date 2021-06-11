---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-06-11 11:01:13'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-06-11 11:01:13'
parent: 0
password: ''
slug: Windows下搭建SSH服务器
status: publish
tags: [SSH, Windows, Powershell]
template: ''
title: Windows下搭建SSH服务器
type: post
---
# 引言

学校里要编辑台式机上的文件，准备用VSCode Remote来做，但是做了才发现貌似默认情况下`Windows`是不配备`ssh`服务器的。

# 解决方案

## 启用`ssh`服务器功能

1. Windows 10 设置中`管理可选功能`
   ![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210611110402.png)
2. 选择添加功能
3. ![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210611110523.png)

## 开机自动启动方案

因为在`powershell`中, 开启服务命令为：

```powershell
start-service sshd
```

我们不妨写一个脚本：

```powershell
#region 关键代码：强迫以管理员权限运行
$currentWi = [Security.Principal.WindowsIdentity]::GetCurrent()
$currentWp = [Security.Principal.WindowsPrincipal]$currentWi
 
if( -not $currentWp.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator))
{
  $boundPara = ($MyInvocation.BoundParameters.Keys | foreach{
     '-{0} {1}' -f  $_ ,$MyInvocation.BoundParameters[$_]} ) -join ' '
  $currentFile = (Resolve-Path  $MyInvocation.InvocationName).Path
 
 $fullPara = $boundPara + ' ' + $args -join ' '
 Start-Process "$psHome\powershell.exe"   -ArgumentList "$currentFile $fullPara"   -verb runas
 return
}
#endregion

Start-Service sshd
echo "SSH Service Status: "
Get-Service sshd
pause
```

扔进启动文件夹即可, 或者创建快捷方式:

```
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe <脚本文件位置>
```
