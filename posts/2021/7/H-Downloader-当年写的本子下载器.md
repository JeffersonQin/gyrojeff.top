---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-07-31 00:52:33'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-31 00:52:33'
parent: 0
password: ''
slug: H-Downloader-当年写的本子下载器
status: publish
tags: [Java]
template: ''
title: H-Downloader | 「当年」写的本子下载器
type: post
---
# 关于 H-Downloader

`H-Downloader`是我于2020年2月写的本子下载器（一年半过去了），整理代码时修了点bug并开源。其可以下载如下网站的本子：
- e-hentai.org
- ja.erocool.com

当时虽然也是拿`IDEA`整的，但是很多地方都有带改进。比方说：依赖是下载的`jar`手动引用的，以及种种，以至于今天，哦不对，是昨天（笑），打开项目的时候都是红叉。下面是这次做的改进：
- 使用`maven`来管理依赖
- `erocool`用了`Cloudflare`做`cdn`，所以对于并发比较敏感。在`H_Downloader.java`当中有一个`MAX_CONCURRENT_THREAD`的常量可以配置下载的并发线程数

# 测试

但是在测试的过程当中仍然发现了问题，比如：
- 未处理`e-hentai`超过40页，即要翻页（有多个简介页）的情况，看心情写。
- 国内`erocool`的主站被墙，`cdn`可能没有被墙。在`analyze`和`download`之间记得开关代理。

主要讲讲我的启动方案吧，感觉这个是很头大的一件事，因为我的`Shadowsocks`不是全局的，而是开了一个本地的代理服务器，而我也懒得在项目中增加`proxy_server`的设置了。基于以上种种，我尝试在`WSL`中跑`GUI`（这一定是一个危险的决定）, 并且还要用`proxychains`（这听起来更加危险了）

## 过程

### 安装`jdk`

首先安装`java`环境。由于`Windows`上一不小心拿了`jdk 16`进行编译（最主要是不想装其他环境了），所以我们就勉为其难地在`Ubuntu`上也装一个吧：

```bash
# 添加PPA
sudo add-apt-repository ppa:linuxuprising/java

sudo apt update

# 安装jdk
sudo apt install oracle-java16-installer
```

如果要卸载：

```bash
sudo add-apt-repository --remove ppa:linuxuprising/java
sudo apt-get remove oracle-java16-installer
```

然后我们测试一下：

```
$ javac --version
javac 16.0.2
```

看来确实有成功装上。`jdk`一般装在这里：

```bash
/usr/lib/jvm/<jdk-name>
```

### 配置`IDEA`启动

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210731010911.png)

然后就是要求输入`jdk`的`version`和`path`，根据上面说的来就行了。

### 尝试启动【前提：配置好X Server，之前的文章有写】

先在`Windows`上`Build`一下，然后`Run`。我们非常惊喜地发现，报错了：

```
/usr/lib/jvm/java-16-oracle/bin/java -Dfile.encoding=UTF-8 -classpath /mnt/c/Users/JeffersonQin/Desktop/H-Downloader/target/classes:/mnt/c/Users/JeffersonQin/.m2/repository/org/jsoup/jsoup/1.14.1/jsoup-1.14.1.jar:/mnt/c/Users/JeffersonQin/.m2/repository/com/itextpdf/itextpdf/5.5.13.2/itextpdf-5.5.13.2.jar edu.sfls.Jeff.JavaDev.HDownloader.App.Main
Exception in thread "main" java.awt.HeadlessException: 
No X11 DISPLAY variable was set,
but this program performed an operation which requires it.
	at java.desktop/java.awt.GraphicsEnvironment.checkHeadless(GraphicsEnvironment.java:165)
	at java.desktop/java.awt.Window.<init>(Window.java:549)
	at java.desktop/java.awt.Frame.<init>(Frame.java:426)
	at java.desktop/javax.swing.JFrame.<init>(JFrame.java:224)
	at edu.sfls.Jeff.JavaDev.HDownloader.Interface.H_Downloader.<init>(H_Downloader.java:22)
	at edu.sfls.Jeff.JavaDev.HDownloader.App.Main.main(Main.java:9)

Process finished with exit code 1
```

它说`No X11 DISPLAY variable was set`，这显然和事实不符。没有关系，经过万能的`stackoverflow`搜索，我们只需要在启动`GUI`前加入这行代码：

```
System.setProperty("java.awt.headless", "false");
```

再次启动，发现还是不行：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210731011315.png)

看到这个报错，候我们又会感到非常困惑：我们的`$DISPLAY`变量明明不是`:0` (注意：这里不行是因为WSL2必须要局域网地址)，但这里却显示是`:0`，肯定启动不了。

经过一番尝试，设置了`/etc/profile`, `~/.profile`, `~/.bashrc`等多个配置文件，还是不行，于是我放弃了这条道路，转而直接进行暴力美学：使用命令行要个屁的IDE。由于每次执行的时候`IDEA`都会直接显示出运行命令，这无疑方便了很多。就像这样：

```bash
/usr/lib/jvm/java-16-oracle/bin/java -Dfile.encoding=UTF-8 -classpath /mnt/c/Users/JeffersonQin/Desktop/H-Downloader/target/classes:/mnt/c/Users/JeffersonQin/.m2/repository/org/jsoup/jsoup/1.14.1/jsoup-1.14.1.jar:/mnt/c/Users/JeffersonQin/.m2/repository/com/itextpdf/itextpdf/5.5.13.2/itextpdf-5.5.13.2.jar edu.sfls.Jeff.JavaDev.HDownloader.App.Main
```

好耶，成功跑起来了：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210731011705.png)

但是我清楚地意识到，现在屁都干不了，因为没开梯子。我尝试前缀加个`proxychains`后点了`analyze`，他很争气地出了结果，但是，中文日文是乱码。

### 解决中文日文乱码问题

安装字体：

```bash
sudo apt-get install fonts-wqy-microhei fonts-wqy-zenhei xfonts-wqy
```

安装完显示便正常了。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210731012146.png)

### 尝试下载

经过不懈地努力测试，我得出了「国内`erocool`的主站被墙，`cdn`可能没有被墙。在`analyze`和`download`之间记得开关代理。」的结论。

方法：我们只需要在下图两个红框框出来的地方反复横跳即可。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210731012352.png)

### 结果图

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210731012830.png)

# 总结与展望

搞了这么多，真的不由得感慨，当初的自己竟然有胆量写`GUI`？我当初竟然能想出`int[1]`这样的数组当作**指针**在**Java**里传参？？？我当初竟然能在`OS X`下面开全局梯子一个错没遇到？？？？现在想想，满满的都是回忆。当初还以这个作为工具，写了扩展接口，当成是`erocool`爬虫的可视化下载界面。那个设计当初竟然只花了五分钟写完，让人不由得看开静态语言和`IDE`的伟大。现在都磨练到可以手写`python`无提示了。

# Reference

- https://www.lainme.com/doku.php/blog/2018/07/如何优雅的在windows_10上装x
- 
