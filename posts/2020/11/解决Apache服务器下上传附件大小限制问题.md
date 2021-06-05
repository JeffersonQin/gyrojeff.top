---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [建站, 技术]
created: '2020-11-18 01:10:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/68.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:29'
parent: 0
password: ''
slug: '37'
status: publish
tags: [Apache, Linux, PHP, Typecho, Ubuntu, VPS]
template: ''
title: 解决Apache服务器下上传附件大小限制问题
type: post
---
Apache服务器+PHP对文件的上传附件大小出事限制为2MB，但我们常常需要更大的文件上传。下面是解决方法：

```bash
locate php.ini
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605843847.png)

找到`apache2`的`php.ini`，编辑：

1. 设置file_uploads = on. 允许通过HTTP进行文件传输，默认值通常已经被设定为on.
2. 设置upload_max_filesize=1000M. 允许上传文件的大小，默认值为2M.
3. 设置post_max_size = 1000M. 通过表达POST给PHP的最大允许值，默认值为8M.

此时文件大小的限制已经被设定完毕，但上传大文件所需时间也会比小文件长，保险起见最好再修改一下上传时间限制：

1. max_execution_time = 600. 每个PHP页面运行的最大时间，以秒为单位，默认值为30秒。
2. max_input_time = 600. 每个PHP页面接收数据所需的最大时间，默认值为60秒。
3. memory_limit = 200M. 每个PHP页面占用的最大内存，默认值为8M.

> 注：vim下的搜索就是`/+搜索内容`

Reference: https://blog.csdn.net/qq_38402294/article/details/90904683

最后，重启服务：

```bash
sudo service apache2 restart
```

