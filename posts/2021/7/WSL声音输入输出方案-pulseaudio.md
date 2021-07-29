---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-07-29 01:03:43'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729111637.png',
  thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-29 17:19:26'
parent: 0
password: ''
slug: WSL声音输入输出方案-pulseaudio
status: publish
tags: [Audio, Linux, WSL]
template: ''
title: WSL声音输入输出方案 - pulseaudio
type: post
---

由于朋友的项目需要用到`audio`组件，而我又安利他了`WSL`，~~为了面子也得把这个搞定~~。

# 配置

1. 下载 [PulseAudio on Windows](https://www.freedesktop.org/wiki/Software/PulseAudio/Ports/Windows/Support/)
2. 设置允许接受的客户端地址: <br/>
   打开`etc\pulse\default.pa`文件，找到第61行, 将 `load-module module-native-protocol-tcp` 修改为
   ```
   load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1 auth-anonymous=1
   ```
3. 设置最大监听时间: <br/>
   打开`etc\pulse\daemon.conf`文件，找到第39行, 修改为
   ```
   exit-idle-time = -1
   ```
4. `WSL`安装`pulseaudio`
   ```bash
   sudo apt install pulseaudio
   ```
5. 设置环境变量
   ```bash
   echo 'export PULSE_SERVER=tcp:127.0.0.1' >> ~/.profile
   ```

# 运行

`powershell`或者`cmd`使用**管理员**运行`bin\pulseaudio.exe`。

# 测试

在`pulseaudio.exe`在后台运行的情况下：

## 扬声器测试

```
ffplay <audio-file>
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729111637.png)

## 麦克风测试

录音：

```bash
arecord demo.wav
```

播放一下：

```bash
ffplay demo.wav
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210729170921.png)

# 写个脚本方便调用

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

<path-to-pulse-audio.exe> --exit-idle-time=-1 -vvvv

pause
```

记得替换掉`<path-to-pulse-audio.exe>`

# Reference

- https://blog.sandtears.com/2020/02/27/wsl-gui-audio-support.html
