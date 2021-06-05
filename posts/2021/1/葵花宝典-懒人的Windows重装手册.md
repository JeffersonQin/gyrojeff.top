---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-28 15:48:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/2ecd2c86621bf59dca5b13e0126f2789ba2e7b72.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-06-06 00:34:55'
parent: 0
password: ''
slug: '272'
status: publish
tags: [Windows, 重装]
template: ''
title: 葵花宝典 | 懒人的Windows重装手册
type: post
---
# Preface

由于我记性实在是差，对于重装这种繁琐的事情必定会丢三落四。遂诞生了这篇葵花宝典（逃）

本文中的所有内容对且仅对本人作参考作用，对阅读者不负任何责任，看到文章请三思而后行。

# 葵花宝典 | 重装手册

在阅读这篇文章之时，想必系统已经被你给操坏了。但是无需担心，你只不过是把系统扬了罢了。通过下面的操作，在数小时之后你便能获得再次把系统扬掉的全新机会 ~

# 1. 备份！

**请一定仔细备份，一定一定一定一定要仔细备份**

- 微信聊天记录：`C:\Users\JeffersonQin\Documents\WeChat Files`
- QQ聊天记录：`C:\Users\JeffersonQin\Documents\Tencent Files`
- osu!整个程序文件夹（即便不推荐我还是使用）：`C:\Users\JeffersonQin\Documents\osu!`
- osu!lazer: `C:\Users\JeffersonQin\AppData\Local\osulazer`
- VSCode设置：`%APPDATA%\Code\User\settings.json`
- Calibre设置：`%APPDATA%\calibre`
- SVP设置：`%APPDATA%\SVP4\settings`
- 弹弹Play整包：`%APPDATA%\弹弹play`
- Wox：`%APPDATA%\Wox\Settings`
- qbittorrent：运行`ToolChain`当中的备份脚本
- SwitchyOmega：导出配置文件
- 磁贴：BackupSML保存好当前布局
- Edge：确认好所有内容已经同步完成
- 记录好GHub里的配置：
  - DPI：1600
  - 侧键 (下)：Mute
  - 侧键 (上)：Win + ↓ （最小化）
  - 侧键 (上)：ESC * 2 （弹弹Play）
- 记录好Potplayer的设置
- 有条件的话去下载一下最新的网卡驱动：https://downloadcenter.intel.com/zh-cn/download/30112/-Windows-10-Wi-Fi-
- 视频文件夹，看需要
- 计算机管理 -> 任务计划程序 -> DDNS 记录下来：
  - 操作：启动程序 `C:\Windows\System32\wsl.exe -e /mnt/d/ToolChain/dnspod-shell/task.sh`
  - 常规
    - 名称：DDNS
    - 账户：JeffersonQin
    - 不管用户是否登录都要运行
    - 使用最高权限
  - 触发器：一次，在2021/2/1的9:00时 - 触发后，无限期地每隔30分钟重复一次，任务运行超过30分钟停止。
- 桌面文件夹全部移动到D盘的OneDrive内，条件允许的话，等待上传完毕。（原因：创建了符号链接）
- **检查文档文件夹**
- **检查下载文件夹**

## WSL备份

现阶段：无。

# 2. 制作系统盘

使用rufus制作，官网：http://rufus.ie/

镜像下载：MDSN我告诉你：https://next.itellyou.cn/

rufus参数：
- 镜像选择：标准Windows安装
- 分区类型：`GPT`
- 目标系统类型：`UEFI`（非`CSM`）
- 文件系统：NTFS

# 3. 插上盘重装

开机按下F12 (因主板而异)，选择启动设备。找到U盘，选择`UEFI Partition 1`分区进行启动，随后就可以看到熟悉的画面（。

找到之前的系统盘，“删除”所有分区，然后选中驱动器，开始安装。

# 4. 整活

1. 首先安装好前面下载的无线网卡驱动（没有的话无所谓），然后进入`Control Panel`配置`DNS`：
   - 223.5.5.5
   - 223.6.6.6
2. 随后，激活Windows：管理员cmd内（每条都要等待运行完再运行下一条）
   ```cmd
   slmgr.vbs -skms kms.cangshui.net
   slmgr.vbs -ato
   slmgr.vbs -dlv
   ```
3. 更改计算机名称：Win+U -> 系统 -> 关于 -> 重命名这台电脑：HQWorkStation
4. Windows Update (打驱动)：Win+U -> 更新和安全 -> Windows更新
5. 经过漫长的等待，重启，然后登录MS账号，随后确认Edge的备份内容。

# 5. 各种nt系统设置

- 使用`ToolChain`里面的修改键位的软件修改好键位
- 点击WiFi -> 属性 -> 专用网络
- 更改卷标：计算机管理 -> 磁盘管理 -> 改
- 远程控制：Win+U -> 系统 -> 远程桌面
- 主题颜色：Win+U -> 个性化 -> 颜色 -> 深色，开启透明效果，金色，不在开始菜单显示颜色，在标题栏和边框显示颜色
- 开始菜单：Win+U -> 个性化 -> 开始 -> 显示更多磁贴
- 不显示cortana图标，显示搜索图标
- 缩放：Win+U -> 系统 -> 显示 -> 主屏125%，副屏175%
- 运行允许文件固定的reg：`D:\文档库\20200502：将文件固定到开始屏幕.reg`
- 开发者选项（否则ps脚本没法运行）：Win+U -> 更新和安全 -> 开发者选项 -> 开发人员模式打开
- Alt+Tab行为：Win+U -> 系统 -> 多任务处理 -> Alt+Tab显示仅打开的窗口
- 配置内网组网的IP：
  - IP地址：169.254.1.10
  - 子网掩码：255.255.0.0
- 文件资源管理器里，Mac映射:
  - Win+R: `\\169.254.1.10`
  - 添加网络映射
- 开机启动项：将`D:\ToolChain\启动`内容复制到`%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup`
- Sandbox，WSL: 搜索栏`启用或关闭Windows功能`
- 显卡配置：确认分辨率、刷新率
- 照片 -> 设置 -> 磁贴 -> 单张照片

# 6. 重打部分驱动

- 声卡（记得在设备管理器里彻底删除/卸载设备）
- 显卡: https://www.nvidia.cn/geforce/drivers/

# 7. 安装软件

## 7.1 ToolChain已有列表

- VNCViewer
- SSR
- AIDA64
- rufus
- HD Tune Pro
- UISpy:
- Win32DiskManager 
- SpaceSniffer
- DiskGenius
- MapKeyBoard
- GoldenDict
- SDFormatter
- BackupSML
- TileIconfier
- SimPadSettings
- GifCam
- Snipaste-2.5.6-Beta-x64 （记得打开设置开机启动）
- mkvtoolnix

## 7.2 从Windows Store安装

- DrawboardPDF
- Python
- Ubuntu
- Speedtest
- CrystalDiskMark Shizuku Edition
- Whiteboard
- MordernFlyouts (Preview)

## 7.3 从已有安装包安装

- Adobe: `D:\下载库\Adobe 2020 WIN 大师版`
- Quicklook若干插件: 
  - 安装方法：https://github.com/QL-Win/QuickLook/wiki/Available-Plugins
  - 名称：`QuickLook.Plugin.*.qlplugin`
- f.lux: `flux-setup.exe`
- AutoHotKey: `AutoHotkey_1.1.33.00_setup.exe`
- 7zip: `7z1900-x64.exe`
- TeamViewer: `TeamViewer_Setup.exe`
- EasyConnect (FDU VPN): `EasyConnectInstaller.exe`
- Dev C++: `devcpp.exe`
- Navicat 15 for MySQL:
  - `navicat150_mysql_cs_x64.exe`
  - `navicat 15.x注册机.7z`
  - 使用方法详见文档库pdf文件，或：https://www.cnblogs.com/nmya/p/12558878.html
- VMWare Workstation 16.0: `VMwareWorkstation16.0.7z`
- 傲梅轻松备份专业版: `傲梅轻松备份专业版.exe`
- DanDanPlay 64位: 已经备份了整个软件包，直接恢复到`%APPDATA%`
- 网易云音乐 UWP: `网易云音乐UWP 不更新版 x64.appx`，先安装证书到`受信任的根证书颁发机构`
- Anki: `anki-2.1.26-windows.exe`
- Furmark: `FurMark_1.21.2.0_Setup.exe`
- Steam: `SteamSetup.exe`
- CrystalDiskInfo Shizuku Edition: `CrystalDiskInfo8_8_9ShizukuUltimate.exe`
- Affinity Designer: `Serif Affinity Designer 1.9.0.876 (x64) Beta Multilingual [sdbeta].7z`
- XMind: `XMind-2020-for-Windows-64bit-10.3.0-202012160243.exe`
- Typora: `typora-setup-x64.exe`
- SAI2: `sai2（终极版).exe`
- iverilog: `iverilog-v11-20201123-x64_setup.exe`
- Mouse Without Borders: `MouseWithoutBordersSetup.msi`

## 7.4 从网络下载最新版本

- Office: [otp.landian.vip](https://otp.landian.vip) 下载Office Tool Plus
- Everything: https://www.voidtools.com/zh-cn/downloads/
- PowerToys: https://github.com/microsoft/PowerToys/releases
- 火绒: https://www.huorong.cn/
- CAJViewer: https://cajviewer.cnki.net/download.html
- FileZilla Client: https://filezilla-project.org/download.php?type=client
- FileZilla Server: https://filezilla-project.org/download.php?type=server
- 向日葵 (记得顺便登陆一下, 官网经常上不上去，安装包本地有): https://sunlogin.oray.com/download/
  - 登录完之后绑定主机
- Calibre: https://calibre-ebook.com/download_windows
  - 导入书库
- 1Password: https://1password.com/downloads/windows/
  - 这个网页下面还有给浏览器安装的链接，一并安装
  - 使用Emergency Kit进行登录
- Wox: https://github.com/Wox-launcher/Wox/releases
- qbittorrent: https://www.qbittorrent.org/
- 百度网盘: https://pan.baidu.com/download
- 微信: https://pc.weixin.qq.com/
- QQ: https://im.qq.com/download/
- 腾讯会议: https://meeting.tencent.com/download-center.html
- Chrome: https://www.google.cn/chrome/
- Github Desktop: https://desktop.github.com/
- COS Browser: https://cloud.tencent.com/document/product/436/38103
- Visual Studio Code: https://code.visualstudio.com/Download
- SVP4 Pro: https://www.svp-team.com/zh/get/
- Visual Studio: https://visualstudio.microsoft.com/zh-hans/
  - 下载Installer
  - 选择需要的内容安装
- Git: https://git-scm.com/downloads
- Nodejs: https://nodejs.org/zh-cn/download/
- Wacom驱动: https://www.wacom.com/zh-cn/support/product-support/drivers
- Intel Driver & Support Assistant: https://downloadcenter.intel.com/download/28425/Intel-Driver-Support-Assistant
- pandoc (记得选msi那个): https://github.com/jgm/pandoc/releases
- Typora: https://typora.io/
- princexml: https://www.princexml.com/download/
  - 记得把安装路径里的`C:\Program Files (x86)\Prince\engine\bin`加入`PATH`, 否则某`VSC`插件可能会出问题
- 印象笔记：https://www.yinxiang.com/download/
- XDpacks: https://xdpacks.com/
  - 下载：FlowKit
- 滴答清单: https://www.dida365.com/about/download/

## 7.5 Steam

- Steam左上角的`Steam` -> 设置 -> 下载 -> Steam库文件夹 -> 添加D盘的`Steam-GalGames`
- WallPaperEngine: 下载好各种东西之后重配Playlist

## 7.6 Edge Apps

- https://squoosh.app/
- https://dict.cnki.net/index#
- https://app.diagrams.net/

# 7.7 DDNS恢复

按照第一节内的配置配置。

# 8. 恢复

将先前的所有备份内容恢复到位。

# 9. 看番

配置看番的一系列东西，见其他文章。

# 10. 配置FTP服务器

见其他文章。

# 11. Onedrive备份 - 符号链接

命令：`mklink /D "<new_dir>" "<dir>"`

需要`mklink`的目录有：
- `C:\Users\JeffersonQin\Desktop` <===> `D:\OneDrive\Desktop (WorkStation)`
- `D:\ToolChain` <===> `D:\OneDrive\ToolChain (x86)`

