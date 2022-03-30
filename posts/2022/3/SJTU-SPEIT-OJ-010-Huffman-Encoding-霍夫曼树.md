---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-30 18:59:10'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-30 18:59:10'
parent: 0
password: ''
slug: SJTU-SPEIT-OJ-010-Huffman-Encoding-霍夫曼树
status: publish
tags: [Huffman, OI]
template: ''
title: SJTU SPEIT OJ 010 Huffman Encoding - 霍夫曼树
type: post
---
## Preface

今天又有一道可靠大仙贝的题，做！

## 题面

### Description

Given a string of a certain length containing English characters, numbers and symbols, output the number of bits of the Huffman-encoded of the string. The input strings is encoded in ASCII with code values from `0x20` to `0x7E`. The length of the input string is `N`

### Input

The first line contains the integerNas described.

The second line contains `N` characters which is the string content. The line is ended with `\n`

### Output

The integer which is the length of huffman-encoded string (unit: bit)

### Sample Input 1

```
12
MT-TECH-TEAM
```

### Sample Output 1

```
33
```

## 题解

前置知识：https://oi-wiki.org/ds/huffman-tree/

这道题就是霍夫曼树的板子，建完树之后就求一下 WPL

$$
	WPL = \sum_{i \in \text{leafs}} w_i \times depth_i
$$

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/huffman-tree-2.png)

注意：

* 读取的时候很坑，具体看代码
* 如果只有一种字符，根据霍夫曼树的定义，应该是 0。但是这道题要特判，答案为 n。

## 代码

```c++
#include <iostream>
#include <queue>
#define int long long

using namespace std;

const int maxn = 1e3+5;

struct node {
	int c, cnt, l, r;
	friend bool operator < (const node &a, const node &b) {
		return a.cnt > b.cnt;
	}
} t[maxn];

int np = 128;

priority_queue<node> q;

int ans = 0;

void dfs(int u, int depth) {
	if (!t[u].l && !t[u].r)
		ans += depth * t[u].cnt;
	if (t[u].l) dfs(t[u].l, depth + 1);
	if (t[u].r) dfs(t[u].r, depth + 1);
}

signed main() {
	int n; cin >> n;
	while (n) {
		char c; cin >> noskipws >> c;
		if (c >= 0x20 && c <= 0x7E) n --;
		else continue;
		t[c].c = c;
		t[c].cnt ++;
	}
	for (int i = 0; i < 128; i ++)
		if (t[i].cnt)
			q.push(t[i]);
	int root = 0;
	while (!q.empty()) {
		if (q.size() == 1) {
			root = q.top().c;
			q.pop();
			break;
		}
		int m1 = q.top().c; q.pop();
		int m2 = q.top().c; q.pop();
		np ++;
		t[np] = (node) {np, t[m1].cnt + t[m2].cnt, m1, m2};
		q.push(t[np]);
	}
	dfs(root, 0);
	if (ans == 0) ans = t[root].cnt;
	cout << ans << endl;
	return 0;
}
```
