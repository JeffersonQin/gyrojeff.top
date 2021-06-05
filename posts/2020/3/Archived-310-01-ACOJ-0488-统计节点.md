---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [OI, 技术]
created: '2020-03-01 22:46:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/oi_bg.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:55:42'
parent: 0
password: ''
slug: '166'
status: publish
tags: [Algorithm, C++, OI, 最小生成树]
template: ''
title: Archived | 310-01-ACOJ-0488-统计节点
type: post
---
# 310-01-ACOJ-0488-统计节点

## 题目大意：

给定一棵有$n$个结点的树，给定树上$m$个点，称作标兵，再给定一个距离范围$k$。求树上有多少点，其本身不是标兵，且到每个标兵的距离都不超过$k$。每条边的长度固定为$1$。

## 题解：

对于这道题，要统计距离所有标兵的距离都不超过$k$的个数。换言之，就是统计节点，对于每一个满足要求的节点，要有其距离最远的标兵的距离不超过$k$。

有了这个想法，不妨构造出叶子节点均为标兵的且包含所有标兵的最小生成树。现在求这棵子树上的直径，或者说是这颗子树上的距离最远的两个端点。

所以题目就转换为统计所有节点，节点满足距离这两个端点都不超过$k$。（证明略，反证法，而且任意一条子树的直径都是可行的）。

<!--more-->

## 代码：

```c++
#include <iostream>

using namespace std;

const int maxn = 100005;
int n, m, k;
int t_point[maxn], head[maxn], dist_1[maxn], dist_2[maxn], dist_3[maxn];
bool isImPoint[maxn];

struct edge {
    int to, next;
} g[maxn << 1];

int ecnt = 2;

void add_edge(int u, int v) {
    g[ecnt] = (edge) {v, head[u]};
    head[u] = ecnt ++;
}

void dfs(int u, int fa, int P) {
    for (int e = head[u]; e != 0; e = g[e].next) {
        int v = g[e].to;
        if (v == fa) continue;
        if (P == 1) dist_1[v] = dist_1[u] + 1;
        if (P == 2) dist_2[v] = dist_2[u] + 1;
        if (P == 3) dist_3[v] = dist_3[u] + 1;
        dfs(v, u, P);
    }
}

int main() {
		cin >> n >> m >> k;
    for (int i = 1; i <= m; i ++) {
      	cin >> t_point[i];
      	isImPoint[t_point[i]] = true;
    }
    for (int i = 1; i < n; i ++) {
      	int u, v; cin >> u >> v;
      	add_edge(u, v); add_edge(v, u);
    }
		dfs(1, 0, 1);
		int node_first = 0;
		for (int i = 1; i <= m; i ++)
				if (dist_1[t_point[i]] >= dist_1[node_first]) node_first = t_point[i];
		dfs(node_first, 0, 2);
		int node_second = 0;
		for (int i = 1; i <= m; i ++)
				if (dist_2[t_point[i]] >= dist_2[node_second]) node_second = t_point[i];
		dfs(node_second, 0, 3);
		int cnt = 0;
		for (int i = 1; i <= n; i ++) {
				if (isImPoint[i]) continue;
				if (dist_2[i] <= k && dist_3[i] <= k)
						cnt ++;
		}
		cout << cnt << endl;
		return 0;
}
```

