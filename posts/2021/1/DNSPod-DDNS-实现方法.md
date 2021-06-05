---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-13 13:01:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/8.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-26 10:02:50'
parent: 0
password: ''
slug: '226'
status: publish
tags: [DDNS, Linux, Ubuntu, WSL, crontab]
template: ''
title: DNSPod DDNS 实现方法
type: post
---
# Preface

之前自己照着Cloudflare的文档实现过DDNS，但其实ddns这个东西GitHub上一搜一大把，所以这次就直接找脚本了（确信

# 搜索

经过一番搜索，我发现了下面这个脚本：https://github.com/anrip/dnspod-shell

# 使用

编辑`ddnspod.sh`，分别修改`/your_real_path/ardnspod`、`arToken`和`arDdnsCheck`为真实信息

为了更好地管理日志，我另外写了一个脚本：

```bash
#!/bin/sh

path="/mnt/c/Users/JeffersonQin/Documents/ToolChain/Tools/dnspod-shell"

date +%Y/%m/%d\ %H:%M:%S >> ${path}/log.txt
${path}/ddnspod.sh >> ${path}/log.txt
```

把上面地`path`改成自己的即可

# 配置crontab

```bash
crontab -e
```

我增加的是：

```bash
0 * * * * /mnt/c/Users/JeffersonQin/Documents/ToolChain/Tools/dnspod-shell/task.sh
```

即每小时更新一次

# 注意事项

对于`crontab`，加不加`sudo`是有区别的，因为`crontab`的配置是对于每个用户来说的，这里**需要**加`sudo`，否则在wsl窗口关闭之后不能自动运行。`crontab`配置完毕后，重启服务：

```bash
sudo service cron restart
```

# WSL中剪切板的小技巧

将输出重定向到剪切板不能用`>>`或`>`，而是要：

```bash
<command> | clip.exe
```

例如：

```bash
cat log.txt | clip.exe
```

就是将`log.txt`的文件内容输出到剪切板内。

