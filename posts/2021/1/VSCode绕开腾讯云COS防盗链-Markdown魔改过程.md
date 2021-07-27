---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-01-30 12:05:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/bilibili-06.jpg',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-02-05 11:16:48'
parent: 0
password: ''
slug: '273'
status: publish
tags: [COS, Markdown, Node.js, TypeScript, VSCode, cURL, npm, 腾讯云]
template: ''
title: VSCode绕开腾讯云COS防盗链 | Markdown魔改过程
type: post
---
# 最新更新

现在已经将其改成了`blogging-tool`，增加了一些主题特定支持的`snippets`和预览功能，但是`fake-referrer`仍然是核心功能之一。

项目页：https://github.com/JeffersonQin/vscode-blogging-tool

# Preface

我还记得一个月前？半个月前？反正就是刚放寒假那会，给一个腾讯云COS的插件改了`bug`，修复了`Windows`下的路劲问题，现在才姑且能用。我的想法其实是想把腾讯云的COS作为图床（毕竟费用还可以）（确信）。在设置了防盗链之后，我发现`VSCode`是无法正常渲染图片的（毕竟就算是基于`Chromium`也怎么可能会设置`referrer`嘛...），所以就想着魔改一把市面上已有的插件来实现此功能。

所以最终魔改后的定位：可以图片请求伪造`referrer`的`markdown preview`插件

# 锁定魔改的插件

第一步便是要锁定需要魔改的插件，但是整个过程十分曲折迂回。一开始安装的插件是`Markdown All in One`: https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one

但是读代码的时候一直觉得很奇怪，那就是即便有个文件叫做`preview.ts`，仍然找不到`WebView`的相关代码。一度以为可能和`html`有关系，便找到了这里：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129224749_ee0b3c7572ea5e0d0cd7942fc6dbedba.png)

最后却发现这个只是将文件导出成`html`（大悲：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129224823_98be760502636b548db5d85f2d5095b7.png)

仔细一想，我禁用了插件，但却发现`VS Code`仍然可以进行Markdown的preview，这才发现原来是`VS Code`本身的功能（我当时直接怒摔键盘（划掉。

所以在这之后，锁定了现在的这个插件：`Markdown Preview Enhanced`：https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced

# Clone代码，运行

使用GitHub Desktop，Clone到了桌面（过程中开着ssr否则速度吃不消），首先尝试直接通过命令行移动：

```powershell
$ wsl
$ mv vscode-markdown-preview-enhanced/ ~
```

但是发现：

```
mv: setting attribute 'system.wsl_case_sensitive' for 'system.wsl_case_sensitive': Invalid argument
```

好像是和wsl大小写不敏感有关，于是乎不搞这些花里胡哨的，准备通过UI界面直接移动文件夹。进入wsl子系统的用户目录：

```bash
explorer .
```

在Windows文件资源管理器内打开这个目录，然后直接拖动文件夹，移动完毕。

用VSCode直接打开这个文件夹：

```bash
code .
```

发现由于在linux环境内，换行符的问题导致所有文件git管理都显示modified，为了消除不必要的困扰以及为了以后一目了然知道哪里是修改过的代码，先commit一次：

```bash
git commit -a -m "Linux Update"
```

首先尝试`npm install`，第一次尝试使用淘宝源：

```bash
cnpm install
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129200257_96e76c027ef79b0efc7a0d0a5c37f80a.png)

表面上好像并没有什么问题，但是下一步进行编译（因为项目使用的是TypeScript）：

```bash
npm run compile
```

发现报错：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129200406_003bf9d2814c808ad3a2b4a9d3cb8173.png)

初步判断可能是因为包老，所以执行了一下Update：

```bash
cnpm update
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129201058_8ae70faf800a6479e03d581c91f9aa48.png)

权限不够，报错，所以加上`sudo`:

```bash
sudo cnpm update
```

仍然报错，版本冲突：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129201143_26fa9641ac3a593023d1f44a310d7ca6.png)

经搜索引擎搜索后无果，遂放弃。

直接选择删除`out`和`node_modules`文件夹，重新使用`npm`安装依赖，不使用`cnpm`：

```bash
proxychains4 npm install
```

由于速度太慢，所以使用了`proxychains4`，有需要的可以看之前的文章。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129200850_ad37f44007056daf59bf2179ef4784f5.png)

可以看到安装成功，但不知道问什么出现了淘宝的地址，大概是装了`cnpm`的原因吧（我也不太懂，毕竟只是为了改个功能并非深挖nodejs，如果有了解的欢迎评论）

开始编译：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129201225_bbc711ff83497da9c7e6495fb03fa583.png)

编译没有问题，所以开始调试：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129201306_a727046e7909e2eb3b3b1bf814fa967e.png)

虽然是在WSL内，但是还是成功地跑起来了，这里感叹一下巨硬牛逼（！

# 搞清大致的结构

快速浏览了一下源码，开始思考切入点：

- 什么时候Preview会更新
- 更新是如何触发的
- 更新的代码写在哪里
- 如何拦截更新

凭着之前写插件的经验，找到了`extension.ts`内：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129201625_0d6d583ab093e6f9764aa1eb01f81aaa.png)

测试了一下，Console确实有输出：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129201701_9f20f05173752f9bfd68d152f42c344c.png)

看到后面的一句：

```TypeScript
contentProvider.update(event.document.uri);
```

`contentProvider`在之前定义了，是`MarkdownPreviewEnhancedView`的实例，暂且不去管它，`event.document.uri`估计是当前正在编辑的窗口的估计指针的类似的东西（纯属口胡，如有误解欢迎指正）

按下Control键单击`update`定位到函数，并增加两条调试语句：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129202111_57a7c4a47ae307a8e82ea7c323c482c8.png)

调试后发现：

- 在没有开启`preview`的情况下指输出`here 1`
- 开启`preview`的情况同时输出`here 2`

得到第一个`if`只是排除了没有预览窗口的情况的结论。开始阅读第二个`if`的代码。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129202436_014e5dde65a489cc2b1ded6d332af4fa.png)

大概可以推测出`this`和`waiting`的含义，估计只是在编辑之后有一段延时再进行渲染，所以主要着眼于:

```TypeScript
this.updateMarkdown(sourceUri);
```

毕竟怎么看都是这句是关键。

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129210152_734ab6df91c3723351436ebf3659f9bb.png)

继续如法炮制，通过打`log`证实了我的猜想：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129210240_b4f931c3e21ee682d933baf9a9b1ad49.png)

看回代码：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129210438_539421a1a54ec01149fb5d3ab5510941.png)

第一个和第二个`if`估计只是业务逻辑上的一些判空，只有后面做这给的注释`presentation mode`我也不知道是啥，所以我姑且就没有理他，通过`log`的输出定位到了`here 5`的地方。现在看之后的代码：

```TypeScript
// not presentation mode
vscode.workspace.openTextDocument(sourceUri).then((document) => {
    const text = document.getText();
    this.previewPostMessage(sourceUri, {
        command: "startParsingMarkdown",
    });

    const preview = this.getPreview(sourceUri);

    engine
        .parseMD(text, {
            isForPreview: true,
            useRelativeFilePath: false,
            hideFrontMatter: false,
            triggeredBySave,
            vscodePreviewPanel: preview,
        })
        .then(({ markdown, html, tocHTML, JSAndCssFiles, yamlConfig }) => {
            // check JSAndCssFiles
            if (
                JSON.stringify(JSAndCssFiles) !==
                    JSON.stringify(this.jsAndCssFilesMaps[sourceUri.fsPath]) ||
                yamlConfig["isPresentationMode"]
            ) {
                this.jsAndCssFilesMaps[sourceUri.fsPath] = JSAndCssFiles;
                // restart iframe
                this.refreshPreview(sourceUri);
            } else {
                this.previewPostMessage(sourceUri, {
                    command: "updateHTML",
                    html,
                    tocHTML,
                    totalLineCount: document.lineCount,
                    sourceUri: sourceUri.toString(),
                    id: yamlConfig.id || "",
                    class: yamlConfig.class || "",
                });
            }
        });
});
```

看到前两行的`text`和`preview`，我也不知道这是在干什么，所以我们需要大胆猜测。胆大心细做好备份，正是API猜测大法的方法论，世界观和方法论是统一的（dbq 胡言乱语ing）。但是通过作者的变量命名，我们发现：`startParsingMarkdown`，`start`这个词好像别有深意！所以我们大胆猜测，这大概就是一个`signal`，和我们要干的事情可能关系不大。为了初步印证我的猜想，我们不妨查看一下`previewPostMessage`到底写了啥：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129211412_cd329c5bba014b7e66198024e481df98.png)

里面关键调用了`VS Code`的`WebView`的`postMessage`方法。扯一句题外话，关于`WebView`，大部分VSC插件都是通过`WebView`来实现界面的。可以通过`Shift + Ctrl (Command) + P`输入`WebView`来呼出调试器。想要确定一个`Panel`是不是`WebView`也很简单：呼得出就是，呼不出就不是。

好，所以好像并没有啥关系，我们不妨输出一下`text`和`preview`：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129212147_631ab54f30c96d81bf476f14aba77266.png)

所以`text`就是文档内容，`preview`倒没看出来啥，啥都不是？（逃

继续看`engine`那行代码，一开始的`.parseMD`好像并不是我们要找的，只是在解析MD构建语法树？后面的`.then()`貌似是在干正事。虽然目测估计正常情况下是执行`else`的代码，但还是打一下`log`确定一下执行的位置：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129213659_d6d759c7352516922adfc8cf35a6a8ab.png)

好，我们离成功只差最后一步了（！因为只有一行代码了（确信

```TypeScript
this.previewPostMessage(sourceUri, {
    command: "updateHTML",
    html,
    tocHTML,
    totalLineCount: document.lineCount,
    sourceUri: sourceUri.toString(),
    id: yamlConfig.id || "",
    class: yamlConfig.class || "",
});
```

我现在非常的迷惑，看到这么一堆参数，但我觉得重要的只有两个：`html`和`tocHTML`，盲猜后面的是目录的`html`？いみふめいです。好，输出不就行了。(顺便整了点花活)

测试文件：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129214218_35507684f52142853804089c9bf04a62.png)

Console输出：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129214455_836bbb317d69b6d348e3550047cedadf.png)

`tocHTML`的输出也应证了我的目录猜想。但是到这里还没完，为了保险起见，我们还需要打开`WebView`的调试器进行进一步的确认：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129214721_f84ab5456e2058250a06d244af6b697d.png)

和先前的结果确实吻合，为了确认这里就是`html`的控制语句，我们不妨把`html`和`tocHTML`都替换成空字符串，看一下渲染效果：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129214938_1afa752a4fb9f2ec871947be394c2da8.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129215010_311ed8d3397895c77a2eeeaf4bab9549.png)

内容确实消失了。

# 明确魔改需求

在搞清楚代码结构以及魔改方法之后，我们就需要明确我们的魔改需求：可以将腾讯云COS中的图片显示出来（之前显示不出来是因为腾讯云我开了防盗链，禁止没有Referrer的请求，而VSCode对图片的请求中并没有Referrer），所以初步的想法有两种：

- 第一种：给请求加上`Referrer`，或者改变`Refer-Policy`
- 第二种：发现有这个网站的图片直接下载到本地，并在渲染前更改渲染的`html`换为本地的。

对于第一种情况：我们不妨直接在编写`markdown`的时候使用`img`标签，里面加入`referrerpolicy="origin"`的属性。但是很遗憾，在`WebView`调试其中我发现，`Referrer`仍然为空，即便有了这个属性：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129220341_678c62eb75871572dce59c89cc032f89.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129220758_ae8224c637a6aec05e6d3591555db92f.png)

注：这里图片请求成功了是因为我暂时关掉了防盗链。

之后，思考到会不会是`<meta>`标签内有这样的信息，但是在查看了主程序的`html`和`WebView`的`html`后都无果，遂放弃了这条思路：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129221214_e9057552e68285124582f1d120494c31.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129221254_ca172fe9e486841ab9c2793414d0e51c.png)

对于第二种方法，我们可以绕开浏览器，通过本地的网络请求来进行实现（`powershell`, `curl`, etc.）

# 开始魔改

首先明确这个插件将图片渲染成`html`的格式：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210129225616_068fe8c8283a2e661e0c8308bf7b863a.png)

发现格式为：

```html
<img src="<link_addr>" alt="<alt_name>">
```

本来想着直接替换的，但是转念一想想到了更好的处理方法：使用现成的库来解析`html`不就得了。

使用搜索引擎搜索了一番之后，我锁定了`cheerio`这个包，正准备安装的时候，看了一下`node_modules`里发现作者貌似已经用了：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130005547_73c1862e8f4dcbb607475f24ecb6c965.png)

于是我们现在是要筛选出`<img>`标签中，`src`属性以特定字符串开头的：

```TypeScript
import * as cheerio from "cheerio";

...

const $ = cheerio.load(html);
$("img").each((_index, element) => {
    if (element.attribs["src"].startsWith("<prefix>")) {
        element.attribs["src"] = "需要改成的东西";
    }
});

html = $.html();
```

反正这里还有非常多的东西需要修改，但是已经初具雏形。接下来要实现的非常重要的功能便是下载功能。由于主力生产在`Windows`上，所以第一反应是`powershell`，然而经过几次的尝试，并未成功（后来发现其实只是忘记开始了而已）

所以最后选择的思路是使用`cURL`。本来我以为`cURL`只有`Linux`上有，在`Windows`上要采取别的实现方式，但是突然之间我发现我多虑了：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130021200_e0356bdd5647af75b61a699e8e1b6140.png)

接下来就是思考如何通过`cURL`的参数来绕过`referrer`的限制：

最后采取的方法：

```bash
curl -o <file_dir> -e "<referrer>" <link>
```

其中`file_dir`为保存的位置，`referrer`字面意思，`link`为下载链接。在Windows里跑了一下，发现也能运行，于是乎我就放心大胆地上了。由于原理和之前失败的`powershell`一样，都是通过`child_process`，所以这次仔细查阅了文档，得到了下面的代码：（至于`console.log`大家忽略就好了（悲，其实那一块就是管道的重定向，问题不是很大）

> 更新：突然想起来，好像也可能是因为`npm`忘记装`child_process`了（我自裁）：
> 
> ```bash
> npm install child_process
> ```
> 
> 如果觉得太慢，可以前面加上`proxychains4`

```TypeScript
import { spawn } from 'child_process'

...

const curl = spawn('curl', ['-o', path.join(fileDir, path.basename(link)), '-e', '"<referrer>"', link]);
curl.stdout.on('data', (data) => {
    console.log(`stdout: ${data}`);
});
curl.stderr.on('data', (data) => {
    console.log(`stderr: ${data}`);
});
curl.on('close', (code) => {
    console.log(`child process exited with code ${code}`);
});
```

第一次跑的时候由于没有像上面那样设置好了`path`所以出现了`write permission denied`的情况。我百思不得其解，于是乎当时就直接跑了一下`pwd`命令（使用同样的方法）：

```TypeScript
const pwd = spawn('pwd');
pwd.stdout.on('data', (data) => {
    console.log(`stdout: ${data}`);
});
pwd.stderr.on('data', (data) => {
    console.log(`stderr: ${data}`);
});
pwd.on('close', (code) => {
    console.log(`child process exited with code ${code}`);
});
```

得到的是`Program Files`里面的路径，于是乎就解释地通了。

现在我的想法是，针对每一个文件，将里面地图片下载在同目录的同名文件夹内。经过搜索和先前的经验，现给出如下代码：

```TypeScript
// Get file directory and file name
let fileDir = vscode.window.activeTextEditor.document.fileName;
let fileName = path.basename(fileDir);

fileDir = fileDir.substr(0, fileDir.length - 3);
fileName = fileName.substr(0, fileName.length - 3);

if (!fs.existsSync(fileDir)) {
    fs.mkdirSync(fileDir);
}
```

注意，随后实在判空，如果文件夹不存在就直接创建新文件夹。做完这部之后其实剩下的只需要拼接起来即可，但是没想到这仍然有问题。

我们会发现，我们最后`html`的`<img>`标签的`src`属性应该是下载下来的文件路径。我一开始看的是`VSCode`官方是如何实现的：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130022521_f487b93c6c89f21723dd3d382fbedace.png)

我发现他是直接用相对路径的。天真无知的我欣喜若狂，以为事情就这样结束了。我如法炮制：一开始给出的路径是：

```TypeScript
`./${fileName}/path.basename(link)`
```

但是运行之后却发现渲染不出来。我当时注意到，文件名之间有空格：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130022734_c1ffc142e342a0441cf414b2b7a11523.png)

我当时便认定大概是空格的问题，为了验证这个想法，我随便新建了一个文件，尝试在图片路径中加入空格，果然：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130022956_efb8ea68e7fb4ea85fd161912af68d75.png)

无法顺利渲染。可能是夜晚使人脑子清醒，我想起了浏览器当中会自动把空格转换成`%20`，于是顺着这个思路去尝试了一番：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130023051_3cc4f30441e4e3669a468a13fa1a9b14.png)

可以正确显示图片，随后我便认定了是空格的原因。搜索后得到了`encodeURI`的处理方法，我将前面的表达式更新为：

```TypeScript
encodeURI(`./${fileName}/path.basename(link)`)
```

但是，调试后仍然发现不行。为了查明问题所在，我决定使用这个插件查看正常情况下图片是如何显示的：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130023302_c33723499d37957140caa622f1674c7b.png)

真相逐渐浮出水面，是`WebView`里面文件协议的问题。以前开发`LaTeX`插件的时候我记得教程里提过这个问题，便去搜索，顺利找到了原文出处：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130023507_1c02eca20f623b08665c7fde4a55a23d.png)

按照前面的方法试了一下，仍然不行，并且发现协议为`vscode-resource://`，而非`vscode-webview-resource://`。综合了一下教程发布的时间，得到了上述协议已经失效的（大概）的结论。

梅开二度，继续搜索，去`VSCode`在`Github`上面的`issues`底下找到了答案：https://github.com/microsoft/vscode/issues/102959

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130023731_7d9bf321c292a4fc5e7142f8b454eb24.png)

注意到，前文使用的`vscode-resource://`协议为`old way`，发布时间为2020年，印证了我的猜想。使用了新方法尝试之后**仍然不行**。思考后决定把之前加上的`encodeURI`删去，才顺利显示，最终这部分的代码：

```TypeScript
// Get file directory and file name
let fileDir = vscode.window.activeTextEditor.document.fileName;
let fileName = path.basename(fileDir);

fileDir = fileDir.substr(0, fileDir.length - 3);
fileName = fileName.substr(0, fileName.length - 3);

if (!fs.existsSync(fileDir)) {
    fs.mkdirSync(fileDir);
}
// replace the image attributes
const $ = cheerio.load(html);
$("img").each((_index, element) => {
    let link = element.attribs["src"];
    if (link.startsWith("<prefix>")) {
        element.attribs["src"] = this.getPreview(sourceUri).webview.asWebviewUri(vscode.Uri.file(path.join(fileDir, path.basename(link)))).toString();
        if (!fs.existsSync(path.join(fileDir, path.basename(link)))) {
            const curl = spawn('curl', ['-o', path.join(fileDir, path.basename(link)), '-e', '"<referrer>"', link]);
            curl.stdout.on('data', (data) => {
                console.log(`stdout: ${data}`);
            });
            curl.stderr.on('data', (data) => {
                console.log(`stderr: ${data}`);
            });
            curl.on('close', (code) => {
                console.log(`child process exited with code ${code}`);
            });
        }
    }
});

html = $.html();
```

至此，魔改告一段落，主要功能基本全部实现：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130024054_8acc3a072a8e48f8713c07c1e0408e72.png)

也许之后可能也会做一个`Configuration`的界面？大概吧，还是看心情。

# 打包安装

在一切准备完毕之后，由于里面涉及到太多个人链接，所以懒得做设置界面和发布了，直接通过`vsix`打包。如果没有安装过`vsix`的话：

```bash
npm install vsix
```

安装完毕之后执行打包命令：

```bash
vsix package
```

发现报错：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130020704_b825778aa7c85a31a855e84b6139cd07.png)

所以带上`--no-yarn`的参数再次打包：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130020859_817bfe116694d99240a01d0dd4598a26.png)

再次报错，通过报错信息定位到`preview-content-provider.ts`文件内，发现问题所在，删除该行：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130021008_db1c27577aaae0833f82e6a36275f14b.png)

估计是之前手抖不小心加的（裂。

最后再次打包，成功：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130021039_f582f14481d8e816c0bb0d4764644861.png)

由于我需要在Windows内安装（等会Linux内也装一把），反正把文件移到别处，然后再在如下位置安装：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130021143_318e798200ccf3ccbc5822e305899454.png)

# 真香，添加设置界面

转念一想，好像添加设置没什么难的，于是就做了。打开`package.json`，找到`configuration`字段，在末尾添加：

```json
"markdown-preview-enhanced.fake-referrer": {
    "description": "The referrer used to download images from restricted servers.",
    "default": "",
    "type": "string"
},
"markdown-preview-enhanced.restricted-prefixes": {
    "description": "If the image link has one of the following prefixes, it will be downloaded using referrer previously configured.",
    "default": [],
    "type": "array"
}
```

看到作者有专门给`config`写过`config.ts`，所以直接到里面去改：

```TypeScript
// referrer config
public readonly fakeReferrer: string;
public readonly restrictedPrefixes: string[];

...

this.fakeReferrer = config.get<string>("fake-referrer");
this.restrictedPrefixes = config.get<string[]>("restricted-prefixes");
```

然后去改一下代码：

```TypeScript
$("img").each((_index, element) => {
    let link = element.attribs["src"];
    this.config.restrictedPrefixes.forEach((prefix) => {
        if (link.startsWith(prefix)) {
            element.attribs["src"] = this.getPreview(sourceUri).webview.asWebviewUri(vscode.Uri.file(path.join(fileDir, path.basename(link)))).toString();
            if (!fs.existsSync(path.join(fileDir, path.basename(link)))) {
                const curl = spawn('curl', ['-o', path.join(fileDir, path.basename(link)), '-e', `"${this.config.fakeReferrer}"`, link]);
                curl.stdout.on('data', (data) => {
                    console.log(`stdout: ${data}`);
                });
                curl.stderr.on('data', (data) => {
                    console.log(`stderr: ${data}`);
                });
                curl.on('close', (code) => {
                    console.log(`child process exited with code ${code}`);
                });
            }
        }
    });
});
```

设置界面：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130113452_8ccfe72a989d8e45065940b70cd79c63.png)

# 准备再次发布

首先把这个项目开源了问题也不大，所以就找到原作者的`GitHub`先`fork`一把：然后再clone到本地，中途用一下`proxychains4`:

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130113721_6c0c8236de9f202ae4d70edb1582e4b0.png)

去`package.json`里做一些些修改：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130114650_6ac0db9fe22401286c1f93bebe0bf149.png)

再对LICENSE和其他设置谁做修改就可以进行发布了：

```bash
vsce publish --no-yarn
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130122846_153521315330b5e0796195bad9789c3e.png)

`Marketplace`的链接：https://marketplace.visualstudio.com/items?itemName=JeffersonQin.blogging-tool

# 一件小事

用这个插件的时候，右键想要导出`pdf`发现提示：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130123731_f282e4c00f6d6ec5d2a570dcdc09d155.png)

搜索，安装：https://www.princexml.com/

但是仍然出现相同提示，转念一想，可能是`PATH`的原因。打开安装目录，找到路径，然后将`C:\Program Files (x86)\Prince\engine\bin`添加进`PATH`，成功解决问题。

# 结束语

这种项目以后谁爱做谁做去，虽然不是特别难，但是和原作者斗智斗勇真的血腥（逃）。而且完全不熟悉前端（可能只是因为我菜），靠着搜索引擎和感觉来写代码（这真的是好文明嘛？）。不过感觉，与其最后只记录答案是如何得到的，不如像现在这样把失败的过程记录下来，反思思考，写下自己脑中的推断过程，可能才会学到更多的东西，帮助到更多的人（大概吧）

