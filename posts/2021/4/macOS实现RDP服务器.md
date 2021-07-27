---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-04-07 01:50:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-06-14 10:12:05'
parent: 0
password: ''
slug: macOS实现RDP服务器
status: publish
tags: [RDP, VNC, macOS]
template: ''
title: macOS实现RDP服务器
type: post
---
这个是在官方repo下的非官方wiki: https://github.com/neutrinolabs/xrdp/wiki/Building-on-OSX-(not-official)

下面为这当前的版本（本人参与贡献）：

# Disclaimer

xrdp on macOS is not officially supported by the xrdp team so far, but we welcome your contributions and volunteers. This document is brought by contributors. Feel free to edit this document but we xrdp team are not involved xrdp on macOS.

To whom want to use xrdp on macOS, **SHARE YOUR KNOWLEDGE WITH EACH OTHER**.

# Building xrdp on OSX

Work in progress.

Since Mac OS 10.10, the OpenSSL headers are not included with the Xcode command line tools. The libraries are available, but they are obsolete (0.9.8). Linking xrdp against those old libraries will fail. You'll need OpenSSL from MacPorts or Homebrew.

## Prerequisites

* Install Xcode command line tools: ```xcode-select --install```
* Install Homebrew: http://brew.sh/
* Install OpenSSL from Homebrew: `brew install openssl`
* Install Automake + Autoconf: `brew install automake`
* Instal Libtool: `brew install libtool`
* Install pkgconfig: `brew install pkgconfig`
* Install nasm: `brew install nasm` (needed by librfxcodec)
* Install XQuartz: https://www.xquartz.org/

## Build xrdp
Get the release tarball xrdp-0.9.1.tar.gz and unpack it.
```bash
./bootstrap
./configure PKG_CONFIG_PATH=/usr/local/opt/openssl/lib/pkgconfig
make
sudo make install
```

## Build xorgxrdp
Get the release tarball xorgrdp-0.2.0.tar.gz and unpack it.
```bash
./bootstrap
./configure PKG_CONFIG_PATH=/opt/X11/lib/pkgconfig
#on Big Sur I had to use following command: ./configure PKG_CONFIG_PATH=/opt/X11/lib/pkgconfig:/usr/local/lib/pkgconfig
make
sudo make install
```

## Ok, whats next?

- The remaining work is to be able to start X with the xrdpxorg module. This is the actual stopper.


- service is in ... 
```
❯ which xrdp
/usr/local/sbin/xrdp
```
- a launchDaemon will be responsible to start the service (not needed for the moment)
- can run `sudo /usr/local/sbin/xrdp` and connect via client.. but produces black screen why? (Perhaps no window manager?)

## Common Troubleshooting Notes

### Unknown Type Error

**Error:** Compiling xrdp fails and the console spits out a bunch of unknown type errors

**Example:** `/usr/include/sys/resource.h:198:2: error: unknown type name 'uint64_t'
        uint64_t ri_system_time;
        ^`

**Fix:** The local libraries in /usr/local/include directory are outdated despite updated libraries being installed on the system as part of the OSX commandline tools. As a workaround to tell g++/clang to ignore the outdated libaries in this directory, change the directory name: 

`# mv /usr/local/include /usr/local/include_old`

Make sure you re-run the configuration generation before compiling again:

```
./bootstrap
./configure PKG_CONFIG_PATH=/usr/local/opt/openssl/lib/pkgconfig
```

**Tested Versions:** MacOS 10.14.3 with XRDP v0.9.9

### 'X11/fonts/libxfont2.h' file not found

**Error Message:**

```
/Library/Developer/CommandLineTools/usr/bin/make all-recursive
Making all in module
CC rdpDraw.lo
In file included from rdpDraw.c:44:
/opt/X11/include/xorg/dixfontstr.h:30:10: fatal error: 'X11/fonts/libxfont2.h' file not found
#include <X11/fonts/libxfont2.h>
^~~~~~~~~~~~~~~~~~~~~~~
1 error generated.
make[3]: *** [rdpDraw.lo] Error 1
make[2]: *** [all-recursive] Error 1
make[1]: *** [all-recursive] Error 1
make: *** [all] Error 2
```

**Fix:** Modify the file `/opt/X11/include/xorg/dixfontstr.h`. Change the include statement into:

```
#include "../X11/fonts/libxfont2.h"
```

**Tested Versions:** MacOS 11.0.1 with XRDP v0.9.15

## Possible Source of Insight

Has anyone looked into the changes made by this user on MacRumors?
https://forums.macrumors.com/threads/how-to-control-your-mac-using-win-rdp-client-xrdp-compiling-guide-on-osx.1770325/

He or she packaged everything up with version 0.6.0 (ancient, yes), but it just works on MacOS Mojave, as long as you disable SIP before installing (haven't tested on Catalina).

(I apologize if this is the wrong place to put this. I couldn't find any open issues that seemed fitting. Feel free to delete/move this where appropriate...)

---

# 奇怪的Troubleshooting

在编译第一步的时候会出问题，这个时候brew装一下就行，包出现的后缀去掉，然后brew install

如果出现Permission Denid的情况，直接找到那个脚本，手动赋权：chmod +x <file_name>

第二部也会出现问题，两种解决方案：

1. 安装MacPort，然后sudo port -v install xorg-server
2. 直接安装XQuartz

这个搞定之后编译还是会出现头文件引用错误：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210407015103.png)

https://github.com/neutrinolabs/xorgxrdp/issues/187

然后就按照[另一篇文章](https://ryancreecy.com/2019/10/29/xrdp-on-mac.html) 把`.so`改为`.dylib`: 

```diff
[Xorg]
name=Xorg
lib=libxup.so
username=ask
password=ask
ip=127.0.0.1
port=-1
code=20

[Xvnc]
name=Xvnc
lib=libvnc.so
username=ask
password=ask
ip=127.0.0.1
port=-1
#xserverbpp=24
#delay_ms=2000

[vnc-any]
name=vnc-any
-lib=libvnc.so
+lib=libvnc.dylib
ip=ask
port=ask5900
username=na
password=ask
#pamusername=asksame
#pampassword=asksame
#pamsessionmng=127.0.0.1
#delay_ms=2000

[neutrinordp-any]
name=neutrinordp-any
lib=libxrdpneutrinordp.so
ip=ask
port=ask3389
username=ask
password=ask
```

# 一切准备就绪！

开启服务：

```
sudo /usr/local/sbin/xrdp
sudo /usr/local/sbin/xrdp-sesman
```

在`Windows`上打开`RDP`, 输入Mac的IP地址，就可以连接了。连接成功后，选第三个选项：`vnc-any`，地址的话就是内网地址或者127.0.0.1，然后用户名密码正常填，然后就进的去了。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210609164950.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210614101107.png)

`username`, `password`, `port`都在上面修改过的那份配置文件中可以修改，这里不多做赘述。

成功截图：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210609165343.png)

# 事后

虽然成功了，但是我们还需要做一些后续的思考。我们会发现这个`rdp`的速度是极其之慢的。我们不妨来做一个简单的计算：2.5k屏，32位真彩，1000Mbps的带宽，这个帧率实在是不忍直视。这个`rdp`是直接套`vnc`，是没有太大意义的，还不如直接上`vnc`，还有优秀的压缩算法。之于方法，详见其他文章。

# 解决方法？

装了一个SwitchResX，调整到了2k分辨率，上VNC，速度奇快，是我现在的生产方案。