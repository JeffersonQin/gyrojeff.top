---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [建站, 技术]
created: '2020-11-15 18:07:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/88.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:56:12'
parent: 0
password: ''
slug: '12'
status: publish
tags: [CSS, Github, HTML, JS, Node.js, Opensource, Typecho, Webpack, 神代綺凜]
template: ''
title: 基于atomicneko魔改搭建Typecho主页（Handsome主题）
type: post
---
# Preface

在搭建本站的时候，又是因为康到了**神代綺凜**大佬的博客主页，遂研究了一番，有了魔改的想法。

[post url="http://moe.best" title="# 神代綺凜の随波逐流" intro="MOE IS JUSTICE ✟ TECHNOLOGY IS POWER<br>萌は正義なのです ✟ テクノロジーがあれば何でもできるです" cover="https://moe.best/usr/img/avatar1.png"/]

# 实现方法

## Fork项目

原项目：[button color="dark" icon="github" url="https:\/\/github.com/amphineko/atomicneko"]amphineko/atomicneko[/button]

## 修改个人信息

主要内容都位于`scr/index.html`和另一个`css`文件当中，直接修改即可。

项目基于Node.js和Webpack打包，具体使用方法在GitHub有写。

我Fork以后的魔改项目：

[button color="dark" icon="github" url="https:\/\/github.com/JeffersonQin/atomicneko"]JeffersonQin/atomicneko[/button]

## 修改Handsome的时光机模板

在魔改好主页的文件之后，我的想法是在时光机页面直接调用那个html，使用`iframe`的方式，结果和原作的方法不谋而合。我将生成的`dist`文件夹放置在`/var/www`的某个目录之下（自己记住，其实直接通过`html`访问都可以），为了将其整合到时光机页面当中，我们则需要修改主题文件目录下的`cross.php`并插入一段`iframe`代码。

```php
<script type="text/javascript">
    // document.domain = "caibaojian.com";
    function setIframeHeight(iframe) {
        if (iframe) {
            var iframeWin = iframe.contentWindow || iframe.contentDocument.parentWindow;
            if (iframeWin.document.body) {
                iframe.height = iframeWin.document.documentElement.scrollHeight || iframeWin.document.body.scrollHeight;
            }
        }
    };

    window.onload = function () {
        setIframeHeight(document.getElementById('external-frame'));
    };
</script>

<iframe src="//gyrojeff.top/intro-chem/index.html" style="width: 100%" frameborder="0" height=1500px scrolling="no" id="external-frame" onload="setIframeHeight(this)" onloadeddata="setIframeHeight(this)" onchange="setIframeHeight(this)"></iframe>
```

# 后记

剩下的就由大家自己魔改啦（去掉自己想去的，仔 细 调 教

反正我魔改之前也从来没玩过php，自己瞎改代码肯定行的（确信）

(/▽＼)

