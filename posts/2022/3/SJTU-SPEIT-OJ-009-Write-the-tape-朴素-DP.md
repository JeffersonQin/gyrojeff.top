---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-30 14:06:51'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-30 14:06:51'
parent: 0
password: ''
slug: SJTU-SPEIT-OJ-009-Write-the-tape-朴素-DP
status: publish
tags: [DP, OI]
template: ''
title: SJTU SPEIT OJ 009 Write the tape - 朴素 DP
type: post
---
## Preface

做了一道可靠大仙贝给的 SJTU 巴院的题。出题人 \*\*\*\*\*\* 啊。题目表述不清。smallest to largest 不是 size 而是 index，所以这句话有什么说的必要吗😅。

## 题面

 <div><h3>Description</h3><p></p><p>In modern data centers, people often use tape drives to store large amounts of data. There are now a total of N files numbered from <code>1</code> to <code>N</code> that need to be written to a single tape. The files are written in order from smallest to largest on different locations on the tape, with zero  spacing between files. The size of i-the file is <code>a_i</code> byte</p><p>Suppose we use a tape drive with <code>m</code> read/write heads, any one of which can write from any position on the tape. The writing speed of the tape head is <code>C</code> bytes per second. These heads can not cross each other, so each read-write head can only select a continuous interval of files to write. The head is allowed to stay idle in the whole process. But one head may not halt until it has completely written all the files initiated by it to write.</p><p>We would like to know the minimum completion time for this batch of files to be completely written.</p><p></p> <h3>Input</h3> <p></p><p>The first line contains 3 integers seperated by space. They are<code>n</code>,<code>m</code>and<code>C</code>as described.</p><p>The second line contains n integers where the i-th integer is<code>a_i</code></p><p>1&lt;=<code>n</code>,<code>m</code>&lt;= 1e2, 1 &lt;=<code>C</code>&lt;= 1e2, 1 &lt;=<code>a_i</code>&lt;= 1e4</p><p></p> <h3>Output</h3> <p></p><p>The integer which is the rounding up of the completion time (unit: second)</p><p></p><div><div><div><h3>Sample Input 1</h3> <pre>5 3 4<br>5 8 3 10 7</pre></div><div><h3>Sample Output 1</h3><pre>4</pre></div></div></div></div>

## 题解

设 $f[i][j]$ 表示前 $i$ 个文件分为 $j$ 组的最小的所有区间的最大值。

$$
	f[i][j] = \min_{0 \leq k \leq i} \{\max (f[i - k][j - 1], \sum_{p=i-k+1}^i a[p])\}
$$

后面的 sigma 可以用前缀和优化

$$
	\sum_{p=i-k+1}^i a[p] = s[i] - s[i - k]
$$

## 代码

```c++
#include <iostream>
#include <cstring>

using namespace std;

const int maxn = 1e2+5;
int a[maxn], s[maxn], f[maxn][maxn];

int up(int n, int d) {
	if (n % d) return n / d + 1;
	return n / d;
}

signed main() {
	int n, m, C; cin >> n >> m >> C;
	for (int i = 1; i <= n; i ++) cin >> a[i];
	memset(f, 0x7f, sizeof(f));
	for (int i = 1; i <= n; i ++) s[i] = s[i - 1] + a[i];
	for (int i = 0; i <= n; i ++) f[i][1] = s[i];
	for (int i = 1; i <= n; i ++)
		for (int j = 2; j <= m; j ++)
			for (int k = 0; k <= i; k ++)
				f[i][j] = min(f[i][j], max(f[i - k][j - 1], s[i] - s[i - k]));
	cout << up(f[n][m], C) << endl;
	return 0;
}
```
