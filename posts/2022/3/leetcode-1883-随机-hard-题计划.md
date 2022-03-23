---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-23 15:55:38'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-23 15:55:38'
parent: 0
password: ''
slug: leetcode-1883-随机-hard-题计划
status: publish
tags: [DP, OI]
template: ''
title: leetcode 1883 — 随机 hard 题计划
type: post
---
## Preface

Leetcode 随机 hard 题计划。来看看 hard 到底 hard 不 hard。~~反正好像是没看出 hard 来。~~

## 题目

https://leetcode-cn.com/problems/minimum-skips-to-arrive-at-meeting-on-time/

## 题解

直接推方程。设 $f[i][j]$ 为前 $i$ 段路跳过 $j$ 个点的最少耗时乘上速度（这样就不用浮点数了）。

$$
	f[i][j] = \min \{up(f[i - 1][j]) + d[i], f[i - 1][j - 1] + d[i]\}
$$

注意：

1. $d[i]$ 是从 $1$ 开始的，消灭 leetcode 的傻逼 IO，从我做起
2. $up$ 是指等待完毕的意思，很简单的，看代码
3. $f[k][k - 1]$ 不能用 $f[k - 1][k - 1]$ 更新，因为不存在，所以要特判
4. 初值的话搞定 $f[i][0]$ 就行，一路加。
5. 注意到：就算休息时间是 $0$，我们照样可以有两种考虑，跳过或者不跳过，反正最后答案会选择跳过次数最少的所以没有问题。

## 代码

非常的丑。特别是状态方程更新的那块：

```c++
// 这个 IO 我直接吐出来
const int maxn = 1e3+5;
long long wait[maxn], d[maxn]; 
long long f[maxn][maxn];
int n = 0;

long long up(long long n, long long d) {
    if (n % d) return (n / d + 1) * d;
    return n;
}

bool exact(long long n, long long d) {
    return !(n % d);
}

class Solution {
public:
    int minSkips(vector<int>& dist, int speed, int hoursBefore) {
        n = dist.size();
        for (int i = 0; i < n; i ++) d[i + 1] = dist[i];
        for (int i = 1; i <= n; i ++)
            f[i][0] = up(f[i - 1][0], speed) + d[i];
        for (int i = 1; i <= n; i ++)
            for (int j = 1; j < i; j ++) {
				if (f[i - 1][j] && f[i - 1][j - 1]) {
					f[i][j] = min(up(f[i - 1][j], speed) + d[i], f[i - 1][j - 1] + d[i]);
					continue;
				}
				if (f[i - 1][j]) {
					f[i][j] = up(f[i - 1][j], speed) + d[i];
					continue;
				}
				if (f[i - 1][j - 1]) {
					f[i][j] = f[i - 1][j - 1] + d[i];
					continue;
				}
            }
        int ans = -1;
        long long cmp = (long long)hoursBefore * speed;
        for (int i = 0; i < n; i ++)
            if (f[n][i] <= cmp && f[n][i]) {
                ans = i;
                break;
            }
        return ans;
    }
};
```
