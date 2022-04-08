---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-04-07 00:26:00'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbChoice: default, thumbDesc: '', thumbSmall: '',
  thumbStyle: default}
modified: '2022-04-07 23:05:11'
parent: 0
password: ''
slug: P2210-Haywire-状压-DP
status: publish
tags: [DP, OI]
template: ''
title: P2210 Haywire - 状压 DP
type: post
---
## 题面

https://www.luogu.com.cn/problem/P2210

## 题解

一开始没反应出来是状压，看了少数题解才知道可以状压。今天姑且 A 掉状压的写法，明天写写看模拟退火。

首先，我们来设状压的状态方程：

$$
	f[i]
$$

* $i$ 代表 $i$ 表示成二进制的时候有 $1$ 那些位所代表的牛组成的集合
* 比方说 $i = 6 = (110)_2$ 代表有 $2, 3$ 号牛组成的集合
* $f[i]$ 代表牛的集合为 $i$ 时对答案产生的贡献。

什么是贡献？这个说法很模棱两可。其实是这样的：两个朋友都在集合里面，那答案必然要包括这对朋友连线的贡献。但是如果只有一头牛在集合里呢？很简单，贡献就是：按照某种最佳排列时，这头牛所处的位置到集合队尾的距离。这么说很抽象，我们举个例子：

$$
	~ * ~ * ~ * ~ \triangle ~ * ~ *
$$

上图中，$*$ 代表无所谓的某头牛，$\triangle$ 代表我们现在考察的单个朋友，在这个状态下，$\triangle$ 对答案的贡献就是 $2$，因为 $\triangle$ 之后还有两头牛.

而如果在这个队列之后紧跟就是 $\triangle$ 的朋友：另一个 $\triangle$，那么在增加前 $\triangle$ 给出的贡献 $2$ 就是这对朋友对答案的贡献了：

$$
	~ * ~ * ~ * ~ \triangle ~ * ~ * ~ \triangle
$$

好。赘述完关于状态的定义，我们来思考状态怎么转移。对于集合 $i$，我们可以枚举位于队列尾端的到底是哪头牛。每次转移我们只需要加上原来的集合中孤立朋友的数量即可。最终给出的代码是做了一些小的优化，稍微难以理解，所以下面给出一个等价的形式，但是时间复杂度多一个 $n$，为 $O(n^22^n)$：

```c++
for (int i = 1; i < (1 << n); i ++) {
	for (int j = 0; j < n; j ++) {
		if ((1 << j) & i) {
			int pending_links = 0;
			int ii = i & ~(1 << j);
			for (int k = 0; k < n; k ++)
				if ((1 << k) & ii)
					pending_links += 3 - 
						(((ii >> nbr[k][0]) & 1) + 
						((ii >> nbr[k][1]) & 1) + 
						((ii >> nbr[k][2]) & 1));
			f[i] = min(f[i], f[ii] + pending_links);
		}
	}
}
```

## 代码

最终给出的代码时间复杂度为 $O(n2^n)$：

```c++
#include <iostream>
#include <cstring>

using namespace std;

const int maxn = 12;

int f[1 << maxn], nbr[maxn + 5][3];

int main() {
	int n; cin >> n;
	for (int i = 0; i < n; i ++) {
		cin >> nbr[i][0] >> nbr[i][1] >> nbr[i][2];
		nbr[i][0] --; nbr[i][1] --; nbr[i][2] --;
	}
	memset(f, 0x7f, sizeof(f));
	f[0] = 0;
	for (int i = 1; i < (1 << n); i ++) {
		int pending_links = 0;
		for (int j = 0; j < n; j ++)
			if ((1 << j) & i)
				pending_links += 3 - 
					(((i >> nbr[j][0]) & 1) + 
					((i >> nbr[j][1]) & 1) + 
					((i >> nbr[j][2]) & 1));
		for (int j = 0; j < n; j ++) {
			if ((1 << j) & i) {
				f[i] = min(f[i], f[i & ~(1 << j)] + pending_links - (3 - 
					(((i >> nbr[j][0]) & 1) + 
					((i >> nbr[j][1]) & 1) + 
					((i >> nbr[j][2]) & 1))) + 
					((i >> nbr[j][0]) & 1) + 
					((i >> nbr[j][1]) & 1) + 
					((i >> nbr[j][2]) & 1));
			}
		}
	}
	cout << f[(1 << n) - 1] << endl;
	return 0;
}
```
