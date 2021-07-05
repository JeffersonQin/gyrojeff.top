---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2021-07-05 16:26:23'
modified: '2021-07-05 16:26:23'
parent: 0
password: ''
slug: ffmpeg-视频抽帧
status: publish
tags: []
template: ''
title: ffmpeg 视频抽帧
type: post
---
![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210705163405.png)

昨天晚上「侦探已死」第一话放松，我躺在床上被白毛戳中xp。prprprpr... 真的很香（wwww

看到同学有在群里面做拼接整出了一张全身像~~（抱枕图）~~，但奈何清晰度太低，遂来了兴致。

找到片源之后进行`ffmpeg`抽帧，命令如下

```bash
ffmpeg -i <source> %d.png
```

会将每一帧保存至`<序号>.png`当中。

注意：如果使用`.jpg`图像质量会大幅下降，虽然输出大小相同，但是`jpg`会进行有损压缩。

---

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210705163045.png)


