---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-12-09 21:39:46'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-12-09 21:39:46'
parent: 0
password: ''
slug: android-emulator-root-and-enable-xposed
status: publish
tags: [Android, Xposed]
template: ''
title: Android Emulator Root 并启用 Xposed 的坑
type: post
---
# Reference

参考：https://juejin.cn/post/6844903840026722311

本文在流程的基础上写了很多必坑方法。

# Prerequisite

* Android Studio
* SuperSU
  * apk - 管理 root 权限
  * zip (recovery) - 用来 root
* XposedInstaller

读者可以自行 Google 下载，或者通过 Reference 链接中提供的链接下载。

# 新建合适的 AVD

## Prerequisite

* Android 8.0 及以下
* Target里 **不带 Google APIs** 的镜像，带 Google APIs 的是不能Root的
* **ABI为x86**，x86_64 会无法正确安装 Xposed

## 过程

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209214406.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209214430.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209214511.png)

上图是我选的，还可以选别的。

# Root

## 命令行启动 AVD

定位到 `ANDROID_SDK_ROOT/tools`

```cmd
emulator -avd <avd-name> -writable-system
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209215506.png)

如果不知道 `<avd-name>` 可以通过下面的命令查看：

```cmd
emulator -list-avds
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209215434.png)

## 安装 SuperSU 与 XposedInstaller

方法一：

将 APK 拖入 Emulator 安装

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209220106.png)

方法二：

```
adb install ...
```

## 获取 Root 权限

```
adb root
adb remount
adb shell
setenforce 0
exit
```

接下来解压 `SuperSU` 的那个 `zip`，将 `x86/su.pie` push 到 emulator 的 `/system/bin` 和 `/system/xbin` 下

```
adb push .../x86/su.pie /system/bin/su
adb push .../x86/su.pie /system/xbin/su
```

然后修改这两个文件的权限，安装su：

```
adb shell
chmod 0755 /system/bin/su
chmod 0755 /system/xbin/su
su --install
su --daemon&
exit
```

完整过程：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209220939.png)

## 打开 SuperSU

选择 `NEW USER` 然后

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209221557.png)

**不要**点 `Continue`，否则整个过程要重新来过。

## 安装 Xposed

打开 XposedInstaller，这里用一下参考文章的图：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209221816.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209221826.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209221842.png)

等搞好以后先点 `NOT NOW`，然后 `adb reboot` 来重启。

重启完毕，进入 XposedInstaller，提示成功：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209222103.png)

## Troubleshooting

### 关于环境变量

注意：`ANDROID_SDK` 可能有多个。`Android-Studio` 对应的那个一般为 `C:\Users\<USER_NAME>\AppData\Local\Android\Sdk`。如果这里没有注意到的话可能会爆

```
PANIC: Broken AVD system path. Check your ANDROID_SDK_ROOT value
```

此外，如果报了上面的错误，设置一下环境变量解决。

方法一：

```cmd
set ANDROID_SDK_ROOT=C:\Users\<USER_NAME>\AppData\Local\Android\Sdk
```

方法二：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20211209215150.png)

### 关于 APK 安装失败

尝试降级，不适用最新版本的 SuperSU，退至旧版。

# 一些注意事项

以后启动 `AVD` 都要通过：

```cmd
emulator -avd <avd-name> -writable-system
```

启动，否则会丧失 Root 权限，会需要重新 Root。

原文提示：
* 不要点电源按钮，直接点×关闭
* 本套root方案只适合Android 9.0以下系统，9.0以上的可以试试用 Magisk 来替代 SuperSu
