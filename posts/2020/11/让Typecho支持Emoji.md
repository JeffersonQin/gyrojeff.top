---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [建站, 技术]
created: '2020-11-15 22:24:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/eb500fb30f2442a75bed2e5add43ad4bd013026b.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:20'
parent: 0
password: ''
slug: '17'
status: publish
tags: [Emoji, MySQL, Navicat, SQL]
template: ''
title: 让Typecho支持Emoji
type: post
---
# Preface

正处于魔改当中，恰巧发现了下面这篇文章，把前因后果都讲清楚了：

[post title="如何让你的Typecho支持Emoji" cover="https://img.laoooo.cn:88/2019/07/18/5d2ff27c66a50.jpeg" url="https://blog.laoooo.cn:88/emoji.html"/]

这篇文章中我主要讲一下使用Navicat更改的方法。

# 实现

## 使用Navicat连接数据库并修改格式

![选择编辑数据库](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605450169.png)

![正确选择编码方式](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605450202.png)

## 执行命令批量解决问题

```sql
use typecho;
alter table typecho_comments convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_contents convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_fields convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_metas convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_options convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_relationships convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_users convert to character set utf8mb4 collate utf8mb4_unicode_ci;
```

## 修改站点配置文件

`config.inc.php`中

```php
$db->addServer(array (
  'host'      =>  localhost,
  'user'      =>  'youruser',
  'password'  =>  'yourpassword',
  'charset'   =>  'utf8mb4', //修改这一行 utf8 -> utf8mb4
  'port'      =>  3306,
  'database'  =>  'yourdatabase'
), Typecho_Db::READ | Typecho_Db::WRITE);
```

# Reference

- [1] https://blog.laoooo.cn:88/emoji.html

