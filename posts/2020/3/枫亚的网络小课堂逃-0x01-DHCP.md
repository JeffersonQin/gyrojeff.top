---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [知识]
created: '2020-03-16 12:30:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/network.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:55:59'
parent: 0
password: ''
slug: '169'
status: publish
tags: [DHCP, 网络]
template: ''
title: 枫亚的网络小课堂（逃） - 0x01 - DHCP
type: post
---
> 「首先下一个定义：DHCP是一种协议」
> 「当电脑连上路由器时」
> 「电脑自己是没有ip地址的」
> 「所以电脑发了一条信息给网关：我要一个ip地址。」
> 「（此处省略各种握手 以及省略UDP层封包 省略冗余报文格式」
> 「（仅表达目的」
> 「草这是不是太草率了一点」
> 「反正就是电脑告诉网关：给劳资一个ip地址，没地址的话我tm没法和其他人通信」
> 「路由器在子网中充当网关的角色」
> 「网关是一种角色」
> 「然后网关告诉电脑：那我这里帮你登记好了，192.168.110.5你可以直接用，在120分钟内（举例）我不会把这个ip提供给其他的设备用，在120分钟后建议你再来向我确认一下这个ip是不是还能用，以免冲突。」
> 「然后电脑就能心安理得地在这120分钟（举例）内用这个ip了。」
> 「如果不开DHCP」
> 「那么就是像jdh那样的网络拓扑」
> 「电脑：哪个憨批让我不开dhcp的，那你tm自己填ip地址，填完我去告诉路由器去。」
> 「路由器收到了电脑的消息，将这个ip从自己DHCP“可用的ip地址池”内删去，这样其他的设备在DHCP时就不会获取到这个地址了」
> 「如果存在静态ip的话」
> 「那么就有可能会出现一种情况」
> 「路由器：刚刚不是有人和我说了192.168.110.6他要用吗……怎么现在又冒出来一个新的人说自己要用192.168.110.6了……我到底把消息发给谁啊……」
> 「然后那么这两台192.168.110.6（声称的ip地址）就都别想上网了。」
> 「所以说这种方法既麻烦 又存在风险」
> 「还不如DHCP来得香嘞」
> 「（此处省略了子网掩码相关内容」
> 「自动获得就是DHCP，手动填写就是静态」

