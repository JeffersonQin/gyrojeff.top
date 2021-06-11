---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [记录]
created: '2021-06-11 11:11:47'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-06-11 11:11:47'
parent: 0
password: ''
slug: 记录-腾讯云COS被打33T
status: publish
tags: [腾讯云, COS]
template: ''
title: 记录 | 腾讯云COS被打33T
type: post
---
# 引言

故事发正在两月底, 今天有时间, 特意来写一下发生了什么.

# 事发

2021年2月28日, 那天我考托福. 出考场的时候收到了微信提醒: 

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210611112638.png)

# 尝试解决

当时我一下子人就傻了, 由于家长还在旁边并不断地询问考试情况, 我只能保持冷静. 在回家地路上做了这么几件事情:
- 搜索之前遇到这种事情的时候别人的解决方法
- 远程控制家里主机确认受害情况

经过搜索, 发现发生这种情况之后基本上要付钱的, 心理直接凉了半截. 但是此时仍然不能慌张, 继续确认受灾程度: 

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/QQMail_1.png)

可以发现在当时中午12点的时候已经被打了16T. 我当时不知道出于什么想法, 没有对其进行任何操作(现在想想真是后怕).

回到家后, 先吃饭, 人是铁饭是钢. 吃完饭后开始思考解决方法. 首先理请情况:
- 我的网站被DDOS攻击
- 被人打了快20T的流量
- 要花很多钱
- 我是学生我没有钱

可以发现拿上面的观点去理论是十分单薄, 基本上不可能为自己争取到利益. 此时, 我想到之前我设置过COS的流量超标预警: 

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/QQMail_6.png)

这个时候我们不妨做一笔简单的计算: 流量是从2/26开始被打的, 那么到2/28中午12点, 至多被打了

$$
  12+24+24 = 60 小时
$$

如果恰好没有触发图中的警报, 那么每10分钟至多被打1GB, 则至多被打了:

$$
  60 小时 / 10 分钟 * 1 \text{GB} = 360 \text {GB}
$$

而我被打了将近20T是远超这个数量级的, 但我却没收到任何警报! 这说明腾讯云也是有责任的, 这是一个非常重要的point.

除此之外, 由于之前为腾讯云COS服务开发/修改发布过两个插件: 
- https://marketplace.visualstudio.com/items?itemName=JeffersonQin.tencent-cloud-cos-upload-image
- https://marketplace.visualstudio.com/items?itemName=JeffersonQin.blogging-tool

同时还写过配置的文章, 我可以称 **我对社区做出过很大的努力和贡献**. 我的目的: **不想付钱**

在明确了 **经过**, **理由**, **目标** 之后, 我拨通了腾讯云的客服电话并详细地阐述了我的情况.

这个过程也一波三折: 

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/QQMail_10.png)

自此, 事情告一段落.

# 事后余波与结果

当日的账单: 

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/QQMail_17.png)

抹平通知:

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210611114553.png)

此外, 由于我当时没有立刻关停COS, 所以除了这两千块钱, 还有额外产生的费用. 第二天我在学校一看, 果不其然:

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/QQMail_19.png)

不过最后也一并抹平了.

# 总结与思考

事发的时候真的是非常的紧张, 特别是在百度了之后发现没有什么好的解决方法之后, 当时心率120+. 不过遇到事情还是要保持冷静, 积极思考对自己有利的事实, 与对方积极沟通, 尝试解决问题. 

大感谢[可靠大鲜贝](https://oreste.moe/)在这次事件当中提供的分析与帮助! (当时人慌了真的思考都慢半拍)

此外, 以后这种预付款的服务一定要慎之又慎, 毕竟1w+的账单可不是开玩笑的(悲). 之前[Sanakey](http://keymoe.com/)阿里云OSS被打的时候我还不以为然, 没想打那么快就降临到了我的头上, 真的是因果报应啊.

最后的最后, 到底是哪个傻逼闲着无聊打我的站??
