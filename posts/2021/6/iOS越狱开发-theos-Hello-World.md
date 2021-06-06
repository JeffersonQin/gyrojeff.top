---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-06-06 14:24:07'
modified: '2021-06-06 14:24:07'
parent: 0
password: ''
slug: iOS越狱开发-theos-Hello-World
status: publish
tags: []
template: ''
title: iOS越狱开发 | theos | Hello World
type: post
---
PART OF THIS DOCUMENT COMES FROM THEOS WIKI ON GITHUB.

# What is theos?

Theos was initially “iphone-framework”, a project created to simplify building code at the command line for iOS devices (primarily jailbroken devices). It later underwent significant changes and became Theos, a flexible [Make](https://www.gnu.org/software/make/)-based build system primarily for jailbreak software development, but also with complete support for building for other supported platforms. Theos runs on, and can build projects for, macOS, iOS, Linux, and Windows (under [Cygwin](https://cygwin.com/) or [Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about)).

As of 2015, the project stalled as Dustin Howett was unable to devote time to it. It was since picked up by [the community](https://github.com/theos/theos/graphs/contributors), adding many desired features and fixing issues. The legacy version of Theos that used to be located at DHowett/theos is permanently preserved on the [legacy](https://github.com/theos/theos/tree/legacy) branch.

There is also documentation available for the original Theos at the [iPhone Dev Wiki](http://iphonedevwiki.net/index.php/Theos), and [theos-ref](https://github.com/theiostream/theos-ref); however be aware that not all information is updated for the changes Theos has undergone recently.

# Installation on iOS (Revised)

This guide will help you install Theos on your iOS jailbroken device.

| Platform | Minimum OS version | Targets supported |
| -------- | ------------------ | ----------------- |
| **iOS**  | 5.0                | iOS               |

All the commands shown on the following instructions are meant to be run as the "user" user, *not* **root**. Similarly, Theos is also meant to be run as a normal user, *not* **root**.

1. Install the following prerequisites:

   - [Sam Bingner’s repository](http://repo.bingner.com/)
   - Theos Dependencies (package on BigBoss repo, relies on the previous repository being installed first)

2. Set up the `THEOS` environment variable:

   ```bash
    echo "export THEOS=/opt/theos" >> ~/.profile
   ```

   For this change to take effect, you must restart your shell. Kill the terminal app in the taskswitcher then re-open the terminal app and do `echo $THEOS` on your shell to check if this is working. If you don't want to log out or re-open, you can simply:

   ```bash
	source ~/.profile
   ```

   Reason of changing `~/theos` into `/opt/theos`: https://github.com/theos/theos/issues/460

3. Clone Theos to your device:

   ```bash
    git clone --recursive https://github.com/theos/theos.git $THEOS
   ```

4. Get the toolchain:

   Theos Dependencies installs iOS Toolchain.

5. Get an iOS SDK:

   You can get patched SDKs from [our SDKs repo](https://github.com/theos/sdks).

   ```bash
    curl -LO https://github.com/theos/sdks/archive/master.zip
    TMP=$(mktemp -d)
    unzip master.zip -d $TMP
    mv $TMP/sdks-master/*.sdk $THEOS/sdks
    rm -r master.zip $TMP
   ```

6. Install the Swift toolchain (optional):

   `swift-toolchain` is on the BigBoss repo.

   Note that the minimum SDK version required to compile Swift code is currently iOS 11.2.

# iphone/tool

```bash
$THEOS/bin/nic.pl
```

接下来你会看到：

```
NIC 2.0 - New Instance Creator
------------------------------
  [1.] iphone/activator_event
  [2.] iphone/activator_listener
  [3.] iphone/application_modern
  [4.] iphone/application_swift
  [5.] iphone/cydget
  [6.] iphone/flipswitch_switch
  [7.] iphone/framework
  [8.] iphone/library
  [9.] iphone/notification_center_widget
  [10.] iphone/notification_center_widget-7up
  [11.] iphone/preference_bundle_modern
  [12.] iphone/theme
  [13.] iphone/tool
  [14.] iphone/tool_swift
  [15.] iphone/tweak
  [16.] iphone/tweak_with_simple_preferences
  [17.] iphone/xpc_service
Choose a Template (required):
```

1. 选择`13`或者`14`, 填入合适的名称

	> 注意：`id`只能由小写字母和`+-.`构成
   
2. 在`Makefile`的最上方加入：

   ```Makefile
	THEOS_DEVICE_IP = 127.0.0.1
   ```

3. 编译部署

   ```bash
	make package install
   ```

   在此之后，`binary`就会被安装到`/usr/local/bin`当中，就可以直接运行。

# Reference

- https://www.youtube.com/watch?v=jGWzc8UIHbk
- https://github.com/theos/theos/wiki/Installation-iOS
- https://github.com/theos/theos/issues/460
