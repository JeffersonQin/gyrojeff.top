---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-04-08 23:50:37'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-04-08 23:50:37'
parent: 0
password: ''
slug: P3572-POI2014-PTA-Little-Bird-DP-单调队列
status: publish
tags: [DP, OI, 单调队列]
template: ''
title: P3572 [POI2014] PTA-Little Bird - DP 单调队列
type: post
---
## 题面

https://www.luogu.com.cn/problem/P3572

## 题解

首先，不难列出方程：$f[i]$ 代表跳到 $i$ 的最小疲劳值

$$
	f[i] = \min_{i - k \leq j < i} \{f[j] + (a[i] \geq a[j])\}
$$

时间复杂度为 $O(qn^2)$，不理想。但是不难发现，这道题可以用单调队列进行优化。为什么？

如下状态一定是更优的：随着 index 递增

* $f$ 不下降（第一维比较）
* $f$ 若相同，同时有 $a$ 不上升（第二维比较）

所以我们就可以用单调队列来维护 index，时间复杂度为 $O(qn)$

注意：

* 初值的设定，详见代码
* 写的顺序和单调队列的模板稍微有一点不同，因为 $i - k \leq j < i$，右边的小于号不是小于等于号，即 $f[i]$ 不能被自己更新，所以调整了一下逻辑块的顺序。

## 代码

```c++
#include <iostream>

using namespace std;

const int maxn = 1e6+5;

int qq, n, k, head, tail, a[maxn], f[maxn], q[maxn];

int main() {
	cin >> n;
	for (int i = 1; i <= n; i ++) cin >> a[i];
	cin >> qq;
	while (qq --> 0) {
		cin >> k;
		head = 1; tail = 1; q[1] = 1;
		for (int i = 2; i <= n; i ++) {
			while (head <= tail && q[head] < i - k) head ++;
			f[i] = f[q[head]] + (a[i] >= a[q[head]]);
			while (head <= tail && (
				f[i] < f[q[tail]] || (
					f[i] == f[q[tail]] && a[i] >= a[q[tail]]
				)
			)) tail --;
			q[++ tail] = i;
		}
		cout << f[n] << endl;
	}
	return 0;
}
```
