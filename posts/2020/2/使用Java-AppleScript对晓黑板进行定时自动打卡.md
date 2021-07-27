---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2020-02-28 18:49:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/49.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-03-31 21:54:38'
parent: 0
password: ''
slug: '130'
status: publish
tags: [AppleScript, Java, OSX, Shell, plist]
template: ''
title: 使用Java, AppleScript对晓黑板进行定时自动打卡
type: post
---
由于我们学校要求每天7点起床打卡，但是实在做不到，遂写了这个脚本。

## 绪论

由于晓黑板不支持网页版，只能使用App进行打卡，所以我使用网易的安卓模拟器，安装App。

## 打卡实现

逻辑非常简单：

- 使用java的Robot类来移动，点击鼠标
- 由于Robot对模拟器输入无效，就使用Applescript键入1
- 再点击一次按钮，完成打卡

<!--more-->

代码：

```java
package edu.sfls.Jeff.JavaDev.App.AutoClockIn;

import java.awt.*;
import java.awt.event.InputEvent;
import java.io.IOException;

public class Main {

    public static void main(String[] args) throws AWTException, InterruptedException, IOException {
        Robot robot = new Robot();
        robot.mouseMove(441, 978);
        robot.mousePress(InputEvent.BUTTON1_DOWN_MASK);
        Thread.sleep(10);
        robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);
        Thread.sleep(1000);
        String[] script = {"osascript", "-e", "tell application \"NemuPlayer\"\n" +
                "\tactivate\n" +
                "end tell\n" +
                "\n" +
                "tell application \"System Events\"\n" +
                "\ttell process \"NemuPlayer\"\n" +
                "\t\ttell window 1\n" +
                "\t\t\tkey code 18\n" +
                "\t\tend tell\n" +
                "\tend tell\n" +
                "end tell"};
        Runtime.getRuntime().exec(script);
        Thread.sleep(1000);
        robot.mouseMove(487, 127);
        robot.mousePress(InputEvent.BUTTON1_DOWN_MASK);
        Thread.sleep(10);
        robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);
    }

}
```

## 打包java文件

首先我们需要通过IDE/命令行打包成可执行jar文件

## 使用AppleScript封装成App

代码：

```shell
do shell script "java -jar /Users/jefferson/Documents/Coding\\ Directory/Apple\\ Script/daka/AutoClockIn.jar"
```

## 使用plist来定时执行

虽然可以用java的办法，但是我有点懒，直接使用Mac OS原生的方法，创建一个plist

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <!-- 名称，要全局唯一 -->
    <key>Label</key>
    <string>com.jefferson.cron.clockin</string>
    <!-- 命令， 第一个为命令，其它为参数-->
    <key>ProgramArguments</key>
    <array>
        <string>open</string>
      	<string>/Users/jefferson/Documents/Coding Directory/Apple Script/daka/daka.app</string>
    </array>
    <!-- 运行时间 -->
    <key>StartCalendarInterval</key>
    <dict>
      <key>Minute</key>
      <integer>01</integer>
      <key>Hour</key>
      <integer>7</integer>
    </dict>
    <!-- 标准输入文件 -->
    <key>StandardInPath</key>
    <string>/Users/jefferson/Documents/run-in-meican.log</string>
    <!-- 标准输出文件 -->
    <key>StandardOutPath</key>
    <string>/Users/jefferson/Documents/run-in-meican.log</string>
    <!-- 标准错误输出文件 -->
    <key>StandardErrorPath</key>
    <string>/Users/jefferson/Documents/run-in-meican.log</string>
  </dict>
</plist>
```

## 写一个shell脚本来刷新

```shell
launchctl unload ~/Library/LaunchAgents/com.jefferson.cron.clockin.plist
sleep 0.5
launchctl load ~/Library/LaunchAgents/com.jefferson.cron.clockin.plist
```

## 给脚本加权限

```bash
sudo chmod +x reset.sh
```

## 运行脚本启动

```
./reset.sh
```

