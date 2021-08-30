---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [随想]
created: '2021-08-21 23:03:36'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-30 13:45:17'
parent: 0
password: ''
slug: 近日又遇到了一些离谱的bug
status: publish
tags: [Bug, 随想]
template: ''
title: 近日又遇到了一些离谱的bug
type: post
---
# 起因

我就是自带测试工程师属性，我就是Bug捕获剂，我他妈就是bug集合体，我不管用什么东西必定会有bug，我就是电子产品克星😅

这篇文章可能会持续更新，毕竟测试工程师经常会进酒吧😅

# 第一批 20210826

* MIX 4 MIUI 12.5，买回来一周不到，就遇到了开机卡死，开机后SIM卡检测被拔出，我甚至没有解锁BL😅
* 当年iPhone 7+，到手一个礼拜，死机，强制重启
* SPX 有次接上键盘就黑屏，拔下来就正常，那次还是在我考试前😅
* SPX 几天待机不用，一开就显示 logo 然后卡死
* 久违地用一下 PicGo，发现了 3 个 bug
  * 报错没有正常 handle：https://github.com/PicGo/vs-picgo/issues/99
  * 插件打包不正常：https://github.com/PicGo/vs-picgo/issues/100
  * WSL 对于有空格的路径没有正确处理：https://github.com/PicGo/PicGo-Core/pull/95
* 好不容易把 bug 都修完了，上传一张图片，第一张很不错，结果第二张上传的时候，Github 炸了😅
* 好不容易换了安卓，用了一下弹弹 Play 安卓版
  * 文件列表每刷新一次，字体大小就会发生变化 https://github.com/xyoye/DanDanPlayForAndroid/issues/96
  * 用中文作为API Token，点击测试直接闪退 https://github.com/xyoye/DanDanPlayForAndroid/issues/95
* 第二次启动VMOS时（第一次退出并清除后台），VMOS的声音就会消失
* 在 SPX 上，点击文件浏览器图标，会弹出 Edge，点击 Edge 图标，还是弹出 Edge

<details>
	<summary>iPhone 11，无故卡死，进不了DFU，卡死循环在开机界面</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/772a23bfa1d22005b60120015e40ab7.jpg">
</details>
<details>
	<summary>用个 BlueStacks for Hyper-V，卡死在开始界面进不去</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/71b8824fd57206bbc87acff2ff21009.png">
</details>
<details>
	<summary>MIUI 控制中心闪现，显示不全</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/f351a67d377870dbc92af016446ecaf.jpg">
</details>
<details>
	<summary>就在我写这篇文章的时候，我搜索微信电脑版的聊天记录中的 "bug"，然后将这个对话框放在第二显示器上并最大化，接着点击第一显示器的显示桌面，穿透显示！</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/59cd6d6d679414689599401ef6a8f5f.png">
</details>
<details>
	<summary>VMOS的设置打错字多了一个顿号</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/ad96e9db1222c6bada8713fac513176.jpg">
</details>
<details>
	<summary>用 Windows，UWP 炸掉，重装以后还炸，连炸两次，我已经能熟练地在一片空白中点击按钮啦！</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130150052_fa97dee8d9f01cbf6416094d28f84e0c.png">
</details>
<details>
	<summary>用微信电脑版，我发了一个「草」，发现 UI 错位了</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/vscode/20210130150522_8e61862875fe1e2699132457765c2067.png">
</details>
<details>
	<summary>用 bilibili，发现港澳台的番剧页面抠图没抠完全</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/783013afa217f922b6d0972fd2271cb.png">
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/d8fcf8dd163be059f06f9dc1c3d9af2.png">
</details>
<details>
	<summary>UWP 不仅 UI 消失，还会随着鼠标位置的不同进行闪烁！</summary>
	<div class="kyt-player" style="height: 889px; width: 400px;">
		<video src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/b1ead5a6702f0533d4ac37f36c695dff.mp4" style="height: auto; width: 100%; background-image: none;" draggable="true" controls="controls">
		</video>
		<div class="play-button" style="display: none;"></div>
	</div>
</details>
<details>
	<summary>MIUI 屏幕旋转时的错位导致分辨率下降</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/ef820647c550050e26081e46a411f16.jpg">
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/32b5aba202e9a63ac7156810d0f58f6.jpg">
</details>
<details>
	<summary>PyQt 5 随着边框距离的调整 在上面两张图的状态中切换</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/0c3fb2f64ccf6af2fad690141745e51.png">
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/3aedc41e40655a35c351bb1367a7fe7.png">
</details>
<details>
	<summary>日常问候张小龙的母亲（1/1）</summary>
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/8049e066f585818885c5a09f0ed6d7b.png">
	<img src="https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/997251d1d4210a09397a37920e98109.png">
</details>
