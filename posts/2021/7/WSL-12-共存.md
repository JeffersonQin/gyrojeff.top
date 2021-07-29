---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-07-29 20:46:09'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-29 20:46:09'
parent: 0
password: ''
slug: WSL-12-共存
status: publish
tags: [Linux, WSL]
template: ''
title: WSL 1/2 共存
type: post
---
昨天把安卓模拟器从Mumu转到了BlueStacks for Hyper-V。做了这个决定之后，我就可以放心大胆地使用WSL 2了。

https://docs.microsoft.com/zh-cn/windows/wsl/install-win10

# 从WSL1开启WSL2方法

1. 安装WSL2所需的必要组件：启用“虚拟机平台”可选功能。可以使用如下命令（或者手动开启）：
   ```powershell
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   ```
2. 下载linux内核更新包：https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

# 设置Linux分发的默认WSL版本

```powershell
wsl --set-default-version 2 # 1 也可以
```

# 切换某一个Linux分发的WSL版本

```powershell
wsl --set-version <distribution name> <versionNumber>
```

# 查看所有安装的WSL Linux分发

```powershell
wsl --list --verbose
```
