---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [OI, 技术]
created: '2020-03-01 22:48:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/oi_bg.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:55:45'
parent: 0
password: ''
slug: '167'
status: publish
tags: [Algorithm, BIT, C++, OI, 树状数组]
template: ''
title: Archived | 310-01-ACOJ-0864-奶牛赛跑
type: post
---
# 310-01-ACOJ-0864 : 奶牛赛跑

## 题目大意：

有$n$头奶牛，在一个圆形的赛跑场地里赛跑。所有奶牛同时从起点出发，奶牛的速度都是匀速的，其中第$i$头牛的速度为$v_i$，跑道的长度为单位$1$。当跑得最快那头奶牛跑完$k$圈之后，比赛就结束了。

有时候，跑得快的奶牛可以比跑得慢的奶牛多绕赛场几圈，从而在一些时刻超过慢的奶牛。这就是最令观众激动的套圈事件了。请问在整个比赛过程中，套圈事件一共会发生多少次呢？

<!--more-->

## 题解：

对于这道题，很显然可以直接求解（小学问题），然后要考虑的是如何降低计算的时间复杂度（原来是$O(n^2)$。

由于每一个奶牛的追击追击次数可以显然得到：$\lfloor \frac {(v_i - v_j)}{v}k\rfloor$，所以要求的就是$\sum_{1≤j<i≤n}\lfloor\frac {v_i - v_j}{v}k\rfloor$。

那么采用整数与小数分离的思想计算：（对于负数向下取整是更小，所以对于$-0. ~…$向下取整为$-1$）

$$
\begin {align*}
&\frac {(v_i-v_j)} {v} k = A + \alpha \\
\Rightarrow~&\frac {v_ik}v - \frac {v_jk}v = A_i + \alpha_i - (A_j + \alpha_j) \\
\Rightarrow~&\sum_{1≤j<i≤n}\lfloor\frac {v_i - v_j}{v}k\rfloor = \sum_{1≤j<i≤n}(A_i - A_j)+\sum_{1≤j<i≤n}\lfloor\alpha_i - \alpha_j\rfloor \\
&\sum_{1≤j<i≤n}(A_i - A_j) \\
=~&\sum_{1≤i≤n}[(i - 1)A_i - (n - i)A_i] = \sum_{1≤i≤n}(i - 1 - n + i) A_i = \sum_{1≤i≤n}(2i - 1 - n)A_i \\

\end {align*}
$$

而可以发现$\sum_{1≤j<i≤n} \lfloor \alpha_i - \alpha_j\rfloor$就是求$\alpha$序列的逆序对个数。

而对于求逆序对同样可以进行优化（因为小数求逆序对需要进行离散化，很烦），令$\alpha_i’ = v_i \times k \mod v_n$，这非常好证明。

## 正解：

```c++
#include <iostream>
#include <algorithm>
#define int long long

using namespace std;

int n, k;
const int maxn = 1000005;
int v[maxn];
int A[maxn], a[maxn];

struct node {
	int id, val;
} B[maxn];

int T[maxn];

int lb(int i) { return i & (-i); }

bool cmp(node a, node b) {
	if (a.val == b.val) return a.id < b.id;
	return a.val < b.val;
}

void add(int i, int d) {
	while (i <= n + 100) {
		T[i] += d;
		i += lb(i);
	}
}

int sum(int i) {
	int ans = 0;
	while (i > 0) {
		ans += T[i];
		i -= lb(i);
	}
	return ans;
}

signed main() {
	cin >> n >> k;
	for (int i = 1; i <= n; i ++) cin >> v[i];
	sort(v + 1, v + 1 + n);
	int ans = 0;
	for (int i = 1; i <= n; i ++) {
		A[i] = v[i] * k / v[n]; a[i] = v[i] * k % v[n];
		ans += (2 * i - 1 - n) * A[i];
		B[i] = (node) {i, a[i]};
	}
	sort(B + 1, B + 1 + n, cmp);
//	for (int i = 1; i <= n; i ++) cout << B[i].id << " " << B[i].val << endl;
	for (int i = 1; i <= n; i ++) {
		int p = sum(B[i].id - 1);
		ans -= (i - 1 - p);
		add(B[i].id, 1);
	}
	cout << ans << endl;
	return 0;
}
```

