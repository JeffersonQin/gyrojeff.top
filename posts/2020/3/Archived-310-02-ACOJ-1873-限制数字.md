---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [OI, 技术]
created: '2020-03-01 23:00:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/oi_bg.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:55:49'
parent: 0
password: ''
slug: '168'
status: publish
tags: [Algorithm, C++, OI, 并查集]
template: ''
title: Archived | 310-02-ACOJ-1873-限制数字
type: post
---
# 310-02-ACOJ-1873-限制数字

## 题目描述：

一个长度为n的大数，用$S_1,..S_n$表示，其中$S\_i$表示数的第i位，$S_1$是数的最高位。
现告诉你一些限制条件，每个条件表示为四个数，$l_1,r_1,l_2,r_2$，即两个长度相同的区间，表示子串$S_{l_1} … S_{r_1}$与$S_{l_2} … S_{r_2}$完全相同。
给定限制条件后，问满足以上所有条件的数有多少个。

<!--more-->

## 题解：

首先考虑没有限制的情况是怎么样的，在没有限制的情况之下，所有的可能的情况应该是第一位数字有九种可能性（不可以是0），其余的各位都有十种可能性，将每位的种数相乘（小学问题）

而现在加上了限制条件，无非就是某几位的可能型变得一样了。

这个时候我们就可以使用**并查集**，如果某两个数字应该是相同的，那么就合并，最后的答案应该就是并查集的个数的$10$连乘（这里没有考虑首位为$9$的情况，这在后文会考虑到）。

但是很遗憾，单独直接使用并查集肯定会超时，时间复杂度为$O(n^2)$。而解决这一问题的方法就是使用二进制优化这一手段。

这里还是使用倍增的思想，我们可以对并查集的每个组编号。而且并查集的储存方式也要进行改变。

定义：

$f[i][j]$是起点为$i$区间$2^j$，即$1<<j$的区间

$fa[i]$表示第$i$个区间的父亲（并查集所需要的数组）

$s[i]$表示以$i$为编号的区间的起点位置（代码中需要知道所以要存）

步骤：

1. 读入 + 预处理数组（这非常好做）
2. 读入限制条件 —> 对“大区间”进行处理把区间简化为尽量少项数的$2^i$之和
3. 下放约束条件：前面只对“大区间”进行了$merge$
   
   现在要**从大到小，对每个区间拆分为两个区间，两两$merge$**
   
   举个形象的例子：$[3,6]$与$[9,12]$这两个区间在并查集当中是合并的，但是$[3,4],[9,10]$和$[5,6],[11,12]$并没有被合并，这就是下放的意义，因为我们最后统计的是长度为$1$的区间的并查集当中的个数
4. 最后第一位的$ans=9$，然后从第二位开始遍历，求答案。

```c++
#include <iostream>
#include <cmath>
#define int long long

using namespace std;

const int maxn = 1e5 + 5;
const int P = 1e9 + 7;
// s[i]表示以i为编号的区间的起始位置, fa[i]表示i的并查集的父亲, f[i][j]是i为起点1<<j为长度的区间的编号
int f[maxn][25], fa[maxn * 25], s[maxn * 25];

int n, m, cnt;

void pre_work() {
	for (int j = 0; j <= log2(n) + 1; j ++)
		for (int i = 1; i <= n; i ++) {
			f[i][j] = ++ cnt;
			fa[cnt] = cnt;
			s[cnt] = i;
		}
}

int get(int x) {
	if (x == fa[x]) return x;
	else return fa[x] = get(fa[x]);
}

void merge(int x, int y, int k) {
	int p = get(f[x][k]), q = get(f[y][k]);
	if (p > q) swap(p, q);
	fa[q] = p;
}

signed main() {
	cin >> n >> m;
	pre_work();
	for (int i = 1; i <= m; i ++) {
		int l1, l2, r1, r2; cin >> l1 >> r1 >> l2 >> r2;
		for (int k = log2(r1 - l1 + 1); k >= 0; k --)
			// 判断这次的区间长度不会超过, 不然下一个k
			if (l1 + (1 << k) - 1 <= r1) {
					merge(l1, l2, k);
					l1 += (1 << k); l2 += (1 << k);
			}
	}
		// 约束条件下放
	int tmp, tmps;
	for (int k = log2(n) + 1;  k; k --)
		for (int i = 1; i + (1 << k) - 1 <= n; i ++) {
			tmp = get(f[i][k]); tmps = s[tmp];
			merge(i, tmps, k - 1); merge(i + (1 << (k - 1)), tmps + (1 << (k - 1)), k - 1);
		}

	int ans = 9;
	for (int i = 2; i <= n; i ++)
		if (fa[f[i][0]] == f[i][0])
			ans = (ans * 10) % P;
	cout << ans << endl;
	return 0;
}
```

这段代码值得注意：

```c++
void merge(int x, int y, int k) {
	int p = get(f[x][k]), q = get(f[y][k]);
	if (p > q) swap(p, q);
	fa[q] = p;
}
```

由于$k$是一样的，所以区间序号上，$p,q$“同阶”，就意味着$x<y\rightarrow p<q;~x=y \rightarrow p=q;~x > y \rightarrow p > q$

在合并区间的过程中，**为了保证父亲区间永远在前面**（因为先统计了第一位的$9$），所以必须要以这个逻辑来写。

下面为**错误**方法，请勿模仿（去掉了高亮）：

```
void merge(int x, int y, int k) {
	int p = get(f[x][k]), q = get(f[y][k]);
	if (p < q) fa[q] = p;
	else fa[p] = q;
}
```

