---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-14 13:57:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/27.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-06-06 14:15:54'
parent: 0
password: ''
slug: '262'
status: publish
tags: [COS, Node.js, VSCode, npm]
template: ''
title: VSCode腾讯云COS插件 | Bug的发现和再发布
type: post
---
# Preface

之前博客启用了腾讯云COS的功能来做图床，而且有插件配合方便上传。之后便想着在VSCode当中实现一样的功能，经过了一番搜索，找到了这个插件：https://github.com/Galen-Yip/tencent-cloud-cos-upload-image

但是，在配置完毕之后，我发现上传路径中混有Windows的`\`。遂修改。

# 修Bug

首先是clone代码，这没啥好多说的。拿到代码发现是`Typescript`写的，我以前是拿JS写的，稍微有点不习惯，但是问题不大。

首先是`npm`一条龙服务，我这里以前就换了淘宝源：

```bash
npm install -g cnpm
```

所以我：

```bash
cnpm install
```

然后，注意到编译的脚本：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610603759.png)

```bash
npm run compile
```

编译后的文件是直接输出到`out`目录

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610603801.png)

接下来就是直接调试。浏览过代码后，定位到存在路径的几个地方，将`\`统一替换成`/`：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610603935.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610603946.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2021/01/1610603988.png)

# 重新打包发布

```bash
npm install -g vsce
```

```bash
vsce publish
```

之后的内容可以参考我以前的[博客](/index.php/archives/268/)

插件主页&安装地址：https://marketplace.visualstudio.com/items?itemName=JeffersonQin.tencent-cloud-cos-upload-image

# 后记

其实整个过程中还遇到了其他很多困难，特别是环境变量的问题导致我误以为powershell脚本有问题，一开始还准备重写。最后总算是顺利解决了这些问题。

不过现在的插件还是有美中不足，那就是配置了防盗链以及跨域CORS访问之后，图片就不能预览了，在以后的更新当中，争取早日解决这个问题。

