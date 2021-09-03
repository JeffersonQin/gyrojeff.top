---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-09-03 09:41:18'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-09-03 09:41:18'
parent: 0
password: ''
slug: 刷题计划-210902-Dijkstra最短路
status: publish
tags: [Dijkstra, OI, 堆]
template: ''
title: 刷题计划 | 210902 | Dijkstra最短路
type: post
---
# Preface

之前做过很多最短路的题，基本上都使用堆优化的dijkstra，包括建立分层图等高级建图方法，但是一直没有系统地写过dijkstra本身是怎么推导的。今天重点想要形象地写一下以前[金牌教练](https://www.zhihu.com/people/marong1204)以前教最短路的方法。

# 单源最短路问题

我们先来简单地描述一下最短路问题：对于一张图 $G$，我们欲求起点 $S$ 到各点地最短距离。

要解决这个问题，我们不妨来先了解一下图论基本方程：对于每个点 $u$，记其到源点的最短距离为 $d(u)$，那么有如下表达：

$$
  d(u) = \min _ v \{ d(v) + w(v, u) \}
$$

上式中，$w(v, u)$ 为边 $(v, u)$ 的权。

定义这张图包含 $n + 1$ 个节点，除了源点记作 $S$ (或者 $u_0$)，其他点记作 $u_i$。

同时，在开始前，我们作如下规定：

$$
  w(u_i, u_i) = 0
$$

$$
  (u, v) \notin G \Rightarrow w(u, v) = \infty
$$

现在，根据上面这个式子，我们不妨全部展开，便可以写出整张图的一张庞大的最短路表格：

$$
\begin{aligned}
  &d(s) = 0 \\ 
  &d(u_1) = \min \{ d(s) + w(s, u_1), d(u_1) + 0, d(u_2) + w(u_2, u_1), \cdots,  d(u_n) + w(u_n, u_1)\} \\ 
  &d(u_2) = \min \{ d(s) + w(s, u_2), d(u_1) + w(u_1, u_2), d(u_2) + 0, \cdots,  d(u_n) + w(u_n, u_2)\} \\
  &\cdots \\
  &d(u_n) = \min \{ d(s) + w(s, u_n), d(u_1) + w(u_1, u_n), d(u_2) + w(u_2, u_n), \cdots , d(u_n) + 0 \}
\end{aligned}
$$

对于如何求最短路，我们可以发现，其实这是一个 $n$ 元的方程组，我们需要做的便是解出这个方程组。

对于如何解这个方程，是一个有趣的问题。我们可以发现，等式的右边都是取最小值的 $\min$ 符号，这无疑大大增加了求解的难度。我们首先考虑一些简化的情况。

$$
  \begin{aligned}
    a = \min \{ w_a', b + w_b, c + w_c \} \\ 
    b = \min \{ w_b', a + w_a, c + w_c \} \\ 
    c = \min \{ w_c', a + w_a, b + w_b \}
  \end{aligned}
$$

上式是一个简化的，除了源点外只有三个节点的情况，$w_a, w_b, w_c, w_a', w_b', w_c'$ 为定值，$a, b, c$ 为未知元。

我们有如下结论：形如上面这样的方程组，即右侧的待取最小的各项中所包含的未知元，也是其他方程的左侧待求值，那么，在所有待求最小值中的已经确定的值当中的最小值，必是那个方程的最小值。这么说可能非常抽象，所以我们不妨将上面的方程中代表常量的字母换成数字，来解释这个过程：

$$
  \begin{aligned}
    a = \min \{ 5, b + 5, c + 7 \} \\ 
    b = \min \{ 7, a + 1, c + 6 \} \\ 
    c = \min \{ 10, a + 4, b + 3 \}
  \end{aligned}
$$

在上面三个式子中，右侧的待求最小值中，只有 $5, 7, 10$ 是确定值，而这些确定值中 $5$ 是最小的，那么 $5$ 就是 $a$ 的值，我们将方程简化为：

$$
  \begin{aligned}
    &a = 5 \\
    &b = \min \{ 7, 6, c + 6 \} = \min \{ 6, c + 6 \} \\ 
    &c = \min \{ 10, 9, b + 3 \} = \min \{ 9, b + 3 \}
  \end{aligned}
$$

现在所有不定方程中的确定值为 $6, 9$，其中最小值为 $6$，所以 $b =  6$。

所以解得：

$$
  \begin{aligned}
    &a = 1 \\ 
    &b = 6 \\
    &c = \min \{ 9, 9 \} = 9
  \end{aligned}
$$

总结一下我们上面的模拟：
* 我们每一次找到全局最小确定值
* 这个值所在的方程的未知数的解便是这个值
* 将这个值代入其他不定方程
* 化简其他不定方程，并将每个方程的若干定值项合并，我们便可以得到一个更小一阶的不定方程组。

接下来，我们不妨使用代数表达一下：设某个状态下简化过后的不定方程 $i$ 为如下表达：

$$
  x_i = \min \{ c_i, \min _ k \{ d_{i, k} + x_k \} \}
$$

上式中：$c_{i, j}$ 表示第 $i$ 个方程的此时化简后的常数项，$d_{i, k}$ 表示第 $i$ 个方程的第 $k$ 个不定项的常数项，$x_k$ 表示第 $k$ 个未知元。

设 $i = i_0$ 时，$c_i$ 即 $c_{i_0}$ 是全局确定项中的最小值最小，即：

$$
  i_0 = \argmin _ {i} c_{i}
$$

则应该有：

$$
  x_{i_0} = c_{i_0}
$$

证明：

$$
  x_{i_0} = \min \{ c_{i_0} , \min _ k \{ d_{i_0, k} + x_k \} \}
$$

因为 $\forall k \in K$ (这里记 $K$ 为所有未知元序数的集合)，有：

$$
  d_{i_0, k} + x_k = d_{i_0, k} + \min \{ \min _ j {c_{k, j}}, \min _ {k'} \{ d_{k, k'} + x_{k'} \} \}
$$

观察上式，令 $d_{i_0, k} + x_k$ 展开后的部分 $d_{k, k'} + x _ {k'}$ 便构成了递归。这个式子可以按照相同方式展开若干次，这个递归可以无穷递归下去然而，我们可以发现，展开式最后的形式必定包含若干个定值项 $c_i$ 和若干个未知元所对应的常数项 $d_j$，可以记作：

$$
  d_{i_0, k} + x_k = \sum _ i c_i + \sum _ j d_j
$$

因为 $\forall i$ (属于未解出的不定方程序号集合)，有：

$$
  c_i \geq c_{i_0}
$$

所以：

$$
  d_{i_0, k} + x_k \geq c_{i_0} + \sum _j d_j \geq c_ {i_0}
$$

则：

$$
  x_{i_0} = \min \{ c_{i_0} , \min _ k \{ d_{i_0, k} + x_k \} \} = c_{i_0}
$$

得证。

此外，大家可以注意到，维持上述逻辑链成立还有一个至关重要的条件：所有涉及到的常数都应该非负，即不能有负边权：

$$
  \forall (u, v) \in G, w(u, v) \geq 0
$$

# 代码实现事项

注意：我们在中间需要求“所有待求最小值中的已经确定的值当中的最小值”，这个本来需要通过遍历所有值来确定，这里我们可以用 **堆 (优先队列)** 这样的数据结构来优化，将时间复杂度从 $O(n)$ 降至 $O(\log n)$，此外，在上面的推导当中，我们虽然使用了不定方程组与若干系数来进行表示，在实现中只需要直接更新 $d(u)$ 的值即可。此外，每次更新值之后，并不需要遍历所有项更新，只需要更新与该节点连接的点的值。

代码：

```C++
#include <iostream>
#include <algorithm>
#include <queue>
#define int long long

using namespace std;

const int maxn = 500005;
int head[maxn], dis[maxn];
bool vis[maxn];
struct edge { int to, next, w; } g[maxn];
struct node {
	int dis, pos;
	friend bool operator < (const node &a, const node &b) {
		return a.dis > b.dis;
	}
};

int ecnt = 2;
int n, m, s;

void add_edge(int u, int v, int w) {
	g[ecnt] = (edge) {v, head[u], w};
	head[u] = ecnt ++;
}

priority_queue<node> q;

void dijkstra() {
	for (int i = 1; i <= n; i ++) dis[i] = 1e10;
	dis[s] = 0;
	q.push((node) {0, s});
	while (!q.empty()) {
		int u = q.top().pos; q.pop();
		if (vis[u]) continue;
		vis[u] = true;
		for (int e = head[u]; e != 0; e = g[e].next) {
			int v = g[e].to;
            if (vis[v]) continue;
			if (dis[v] > dis[u] + g[e].w) {
				dis[v] = dis[u] + g[e].w;
				q.push((node) {dis[v], v});
			}
		}
	}
}

signed main() {
	cin >> n >> m >> s;
	for (int i = 1; i <= m; i ++) {
		int u, v, w; cin >> u >> v >> w;
		add_edge(u, v, w);
	}
	dijkstra();
	for (int i = 1; i <= n; i ++)
		cout << (dis[i] == 1e10 ? 2147483647 : dis[i]) << (i == n ? "\n" : " ");
	return 0;
}
```

测试传送门：
* [P3371 【模板】单源最短路径（弱化版）](https://www.luogu.com.cn/problem/P3371)
* [P4779 【模板】单源最短路径（标准版）](https://www.luogu.com.cn/problem/P4779)
