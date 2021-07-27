---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [OI, 技术]
created: '2020-03-01 21:05:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/oi_bg.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:55:34'
parent: 0
password: ''
slug: '156'
status: publish
tags: [Algorithm, C++, DP, OI]
template: ''
title: Archived | 307-13-子集枚举DP
type: post
---
# 307-13-14-子集枚举DP

## P3959 宝藏

### 题目描述

参与考古挖掘的小明得到了一份藏宝图，藏宝图上标出了$n$个深埋在地下的宝藏屋， 也给出了这$n$个宝藏屋之间可供开发的$m$条道路和它们的长度。

小明决心亲自前往挖掘所有宝藏屋中的宝藏。但是，每个宝藏屋距离地面都很远， 也就是说，从地面打通一条到某个宝藏屋的道路是很困难的，而开发宝藏屋之间的道路 则相对容易很多。

小明的决心感动了考古挖掘的赞助商，赞助商决定免费赞助他打通一条从地面到某 个宝藏屋的通道，通往哪个宝藏屋则由小明来决定。

在此基础上，小明还需要考虑如何开凿宝藏屋之间的道路。已经开凿出的道路可以 任意通行不消耗代价。每开凿出一条新道路，小明就会与考古队一起挖掘出由该条道路 所能到达的宝藏屋的宝藏。另外，小明不想开发无用道路，即两个已经被挖掘过的宝藏 屋之间的道路无需再开发。

新开发一条道路的代价是：$L \times K$

$L$代表这条道路的长度，$K$代表从赞助商帮你打通的宝藏屋到这条道路起点的宝藏屋所经过的 宝藏屋的数量（包括赞助商帮你打通的宝藏屋和这条道路起点的宝藏屋） 。

请你编写程序为小明选定由赞助商打通的宝藏屋和之后开凿的道路，使得工程总代 价最小，并输出这个最小值。

<!--more-->

### 输入格式

第一行两个用空格分离的正整数$n,m$，代表宝藏屋的个数和道路数。

接下来$m$行，每行三个用空格分离的正整数，分别是由一条道路连接的两个宝藏 屋的编号（编号为$1  \sim n$），和这条道路的长度 $v$。

### 输出格式

一个正整数，表示最小的总代价。

<div data-v-44182ca5="" data-v-e7be449e="" class="sample-wrap sample" show-idx="true"><div data-v-c1761324="" data-v-44182ca5="" class="input"><strong data-v-c1761324="">输入 #1</strong> <button data-v-6b5ddc32="" data-v-c1761324="" type="button" class="copy-btn lfe-form-sz-middle" style="border-color: rgb(52, 152, 219); color: rgb(52, 152, 219); background-color: rgba(52, 152, 219, 0);">
    复制
  </button> <pre data-v-c1761324="">4 5 
1 2 1 
1 3 3 
1 4 1 
2 3 4 
3 4 1 
 </pre></div> <div data-v-c1761324="" data-v-44182ca5="" class="output"><strong data-v-c1761324="">输出 #1</strong> <button data-v-6b5ddc32="" data-v-c1761324="" type="button" class="copy-btn lfe-form-sz-middle" style="border-color: rgb(52, 152, 219); color: rgb(52, 152, 219); background-color: rgba(52, 152, 219, 0);">
    复制
  </button> <pre data-v-c1761324="">4</pre></div></div>

<div data-v-44182ca5="" data-v-e7be449e="" class="sample-wrap sample" show-idx="true"><div data-v-c1761324="" data-v-44182ca5="" class="input"><strong data-v-c1761324="">输入 #2</strong> <button data-v-6b5ddc32="" data-v-c1761324="" type="button" class="copy-btn lfe-form-sz-middle" style="border-color: rgb(52, 152, 219); color: rgb(52, 152, 219); background-color: rgba(52, 152, 219, 0);">
    复制
  </button> <pre data-v-c1761324="">4 5 
1 2 1 
1 3 3 
1 4 1 
2 3 4 
3 4 2  </pre></div> <div data-v-c1761324="" data-v-44182ca5="" class="output"><strong data-v-c1761324="">输出 #2</strong> <button data-v-6b5ddc32="" data-v-c1761324="" type="button" class="copy-btn lfe-form-sz-middle" style="border-color: rgb(52, 152, 219); color: rgb(52, 152, 219); background-color: rgba(52, 152, 219, 0);">
    复制
  </button> <pre data-v-c1761324="">5</pre></div></div>

### 说明/提示

![img](https://cdn.luogu.org/upload/pic/10868.png)

【样例解释1】

小明选定让赞助商打通了1 11 号宝藏屋。小明开发了道路$1 \to 2$，挖掘了$2$号宝 藏。开发了道路$1 \to 4$，挖掘了$4$号宝藏。还开发了道路$4\to 3$，挖掘了$3$号宝 藏。工程总代价为：$1×1+1×1+1×2=4$

【样例解释2】

小明选定让赞助商打通了$1$号宝藏屋。小明开发了道路$1\to 2$，挖掘了$2$号宝 藏。开发了道路$1 \to 3$，挖掘了$3$号宝藏。还开发了道路$1\to 4$，挖掘了$4$号宝 藏。工程总代价为：$1×1+3×1+1×1=5$

【数据规模与约定】

对于$20\%$的数据： 保证输入是一棵树，$1≤n≤8$，$v≤5000$且所有的$v$都相等。

对于$40\%$的数据：$ 1≤n≤81$，$0≤m≤10000$，$v≤5000$且所有的$v$都相等。

对于$70\%$的数据： $1≤n≤8$，$0≤m≤1000$，$v≤5000$

对于$100\%$的数据： $1≤n≤12$，$0≤m≤1000$，$v≤500000$

### 题解

我们可以想到，将整张图分为一块一块的（分层图），类似于下面这样的金字塔的形状：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608994487.png)

这样做的意义是我们只允许每层的元素与上一层相连，就达到了题目的用意，这样每个节点的$L\times K$就是$w(u,v) \times lv,~u\in S_{lv-1},~v\in S_{lv}$，其中$lv0$的只能有一个节点，就是赞助商免费送的根，总结下来可以得到下面这张构图的结论：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608994500.png)

定义$f(lv,S)$，$S$为一个点集，$f(lv,S)$表示的是截至到$0\sim lv$层中所有点分别为$S$的这$lv + 1$层的总共的花费是多少。那么会有以下的式子：（枚举$T$为$S$的真子集）

$$
f(lv,S) = \min_{T\subset S} \Big \{ f(lv - 1,S-T) + lv \times b\sum_{u\in T}\min_{a\in S-T}w(u, a)\Big \}
$$

但是仔细观察上式，还是存在问题，就是$\sum_{u\in T}\min_{a\in S-T}w(u,a)$的部分，其会出现下图的情况（单单对于$P9$而言的图）：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608994511.png)

换句话说，其可能不止访问上一层的，还会访问更加上面的点。但是我们分析后会发现，这无伤大雅，因为我们假设访问上面的点为$u$，层数为$lv’$，且$lv’ < lv - 1$，则此时的费用为：

$$
L'\times K = w(u,P_9) \times lv > w(u,P_9)\times (lv' + 1)
$$

很好理解，这种状态不是最优的，所以在方程迭代的过程中，这种状态会被更好地状态所替代。

接下来，就是研究方程的边界问题：

$$
f_{i\in V} (0,\{i\}) = 0,~f_{S \subset V,~|S| ≥2}(0,S) = \infty
$$

或者我们可以化为：

$$
f(i,S) = 
\begin {cases}
~0~~~~~~~i\in V,S=\{i\} \\
\infty~~~~~~\text{otherwise}
\end {cases}
$$

接下来我们可以思考一下这个方程的实现，因为表示集合与判断属于或者包含的关系并不是一件简单的事情。最终，我们想到可以使用二进制数来表示集合，例如$1,~2,~3,~7,~8$都有，$4,~5,~6$没有的集合可以表示为：

$$
\begin {align*}
S=(&~11000111~)_2 \\
&\downarrow\downarrow\downarrow\downarrow\downarrow\downarrow\downarrow\downarrow \\
&~87654321
\end {align*}
$$

用这种思想，我们可以得到以下的结论：

1. 要判断$T$是否为$S$的子集，只需要判断：
   
   $(S~|~T)==S$即可。
2. 如果要判断$e$是否在集合$S$当中，只需要判断
   
   $(S ~|~(1~\text{<<}~e)) == S$即可。
3. 想要求$S-T$这个集合：
   
   $S-T=S~\text{^}~T$。
4. 迭代递归求子集：
   
   $T = (T-1) ~\&~ S$，其中$T$为子集$S$为全集，$T$的初始值为$S$，这样可以枚举$S$的所有子集，时间复杂度为$O(3^n)$而不是$O(4^n)$。

所以可以基于方程，给出代码：

```c++
#include <iostream>
#define inf 1e9;
using namespace std;

typedef long long ll;

ll n, m, w[13][(1 << 12) + 5], f[13][(1 << 12) + 5];

int main() {
    cin >> n >> m;
    if (n == 1 && m == 0) {
        cout << 0 << endl;
        return 0;
    }
    for (ll i = 0; i < 13; i ++)
        for (ll s = 0; s < (1 << n); s ++)
            w[i][s] = inf;
    while (m --> 0) {
        ll u, v, len; cin >> u >> v >> len;
        u --; v --;
        w[u][1 << v] = min(w[u][1 << v], len);
        w[v][1 << u] = min(w[v][1 << u], len);
    }
    for (ll i = 0; i < n; i ++)
        for (ll s = 1; s < (1 << n); s ++) {
            ll lb = s & (-s);
            w[i][s] = min(w[i][s - lb], w[i][lb]);
        }
    for (ll i = 0; i < n; i ++) {
        for (ll s = 0; s < (1 << n); s ++)
            f[i][s] = inf;
    }
    for (ll j = 0; j < n; j ++)
        f[0][1 << j] = 0;
    for (ll lv = 1; lv < n; lv ++) {
        for (ll s = 0; s < (1 << n); s ++) {
            for (ll t = s - 1; t > 0; t = (t - 1) & s) {
                ll sum = 0;
                for (ll x = 0; x < n; x ++)
                    if ((t | (1 << x)) == t) sum += w[x][s ^ t];
                f[lv][s] = min(f[lv][s], f[lv - 1][s ^ t] + lv * sum);
            }
        }
    }
    ll ans = inf;
    for (ll i = 1; i < n; i ++)
        ans = min(ans, f[i][(1 << n) - 1]);
    cout << ans << endl;
    return 0;
}
```

## P3052 [USACO12MAR]摩天大楼里的奶牛Cows in a Skyscraper

<div class="lg-content-left">
<div class="lg-article am-g">
<h3>题目描述</h3>
<p>A little known fact about Bessie and friends is that they love stair climbing races. A better known fact is that cows really don't like going down stairs. So after the cows finish racing to the top of their favorite skyscraper, they had a problem. Refusing to climb back down using the stairs, the cows are forced to use the elevator in order to get back to the ground floor.</p>
<p>The elevator has a maximum weight capacity of W (1 &lt;= W &lt;= 100,000,000) pounds and cow i weighs C_i (1 &lt;= C_i &lt;= W) pounds. Please help Bessie figure out how to get all the N (1 &lt;= N &lt;= 18) of the cows to the ground floor using the least number of elevator rides. The sum of the weights of the cows on each elevator ride must be no larger than W.</p>
<p>给出n个物品，体积为w[i]，现把其分成若干组，要求每组总体积&lt;=W，问最小分组。(n&lt;=18)</p>
<h2>输入输出格式</h2>
<strong>输入格式：</strong><br>
<p></p><p>* Line 1: N and W separated by a space.</p>
<p>* Lines 2..1+N: Line i+1 contains the integer C_i, giving the weight of one of the cows.</p><p></p>
<strong>输出格式：</strong><br>
<p></p><p>* A single integer, R, indicating the minimum number of elevator rides needed.</p>
<p>one of the R trips down the elevator. </p><p></p>
<h3>输入输出样例</h3>
<div class="am-g">
<div class="am-u-md-6 copy-region">
<strong>输入样例#1：</strong>
<a class="am-badge am-radius lg-bg-orange sample-copy">复制</a>
<pre>4 10 
5 
6 
3 
7 
</pre>
</div>
<div class="am-u-md-6 copy-region">
<strong>输出样例#1：</strong>
<a class="am-badge am-radius lg-bg-orange sample-copy">复制</a>
<pre>3 </pre>
</div>
</div>
<h2>说明</h2>
<p>There are four cows weighing 5, 6, 3, and 7 pounds. The elevator has a maximum weight capacity of 10 pounds.</p>
<p>We can put the cow weighing 3 on the same elevator as any other cow but the other three cows are too heavy to be combined. For the solution above, elevator ride 1 involves cow #1 and #3, elevator ride 2 involves cow #2, and elevator ride 3 involves cow #4. Several other solutions are possible for this input.</p>
</div>
<div class="lg-article-sub am-g" id="sub" style="display: none;"></div>
</div>

### 题解

这道题使用一种另类的贪心想法，<font color="#FF0000">这种贪心的转移状态比较特殊</font>，在这里设了两个转移方程，分别为：

$$
\left [\begin {matrix} f(S) \\ g(S) \end {matrix}\right ] = \min_{u\in S}
\begin {cases}
\left [\begin {matrix} f(\complement _S\{u\}) \\g(\complement_S\{u\}) +w(u) \end {matrix}\right ]  & g(\complement_S\{u\}) + w(u) ≤C
\\
\left [\begin {matrix} f(\complement_S\{u\})  + 1 \\ w(u) \end {matrix}\right ] & \text{otherwise}
\end {cases}
$$

其中是将

$$
\left [\begin {matrix} f(S) \\g(S) \end {matrix}\right ]
$$

定义为一个关于$S$矩阵。$f(S)$代表以$S$为集合的牛已经关闭的电梯的数量，即电梯不再对牛开放，已经完成运送了，$g(S)$表示这集合$S$当中，除了$f(S)$中已经被送走的牛，剩下的牛的重量。<font color="#FF0000">注</font>：其中，一定存在$g(S) < C$（没有一头的重量超过$C$）。

有了上述定义，这个方程非常容易理解，所以只需要进行子集枚举的背包就可以了，故时间复杂度为$O(n3^n)$。

AC代码：

```c++
#include <iostream>
#include <algorithm>

using namespace std;
pair<int, int> f[1 << 23];
int n, c, w[1 << 23];

int main() {
    cin >> n >> c;
    int all = (1 << n) - 1;
    for (int i = 0; i < n; i ++)
        cin >> w[i];
    for (int s = 1; s <= all; s ++) {
        f[s] = make_pair(n, 0);
        for (int u = 0; u < n; u ++) {
            if (s & (1 << u)) {
                int x = f[s ^ (1 << u)].first;
                int y = f[s ^ (1 << u)].second;
                if (y + w[u] <= c) {
                    f[s] = min(f[s], make_pair(x, y + w[u]));
                } else {
                    f[s] = min(f[s], make_pair(x + 1, w[u]));
                }
            }
        }
    }
    cout << f[all].first + (f[all].second ? 1 : 0) << endl;
    return 0;
}
```

