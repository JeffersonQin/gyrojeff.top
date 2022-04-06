---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-04-05 23:04:18'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-04-05 23:04:18'
parent: 0
password: ''
slug: leetcode-780-到达终点-随机跳题计划
status: publish
tags: [OI]
template: ''
title: leetcode 780 到达终点 - 随机跳题计划
type: post
---
## 题面

https://leetcode-cn.com/problems/reaching-points/

## 题解

就是一道思维题。看一下数据范围：$1e9$，正做肯定是不行的。借鉴小升初时候的失败经验，这题必然是从后往前推。怎么从后往前推呢？很简单：

$$
	(x, y) \leftarrow (x - y, y), x > y
$$

只有这么一种情况。

但是如果我们摁写会 TLE，所以这里做一下取模：

$$
	(x \bmod y, y) \leftarrow (x, y), x > y
$$

但是，如果直接这么做还是会 WA，因为最终结束的答案可能并不是 $x \bmod y$，而是 $x \bmod y + ny$，所以我们在计算的时候还需要设定一个最小值，然后做一下简单的加减乘除就可以了。

## 代码

```c++
class Solution {
public:
    int pm(int x, int y, int m) {
        if (x == y) return 0;
        int ans = y;
        if (x % y) ans = x % y;
        int ans2 = 0;
        if (ans < m) {
            if (ans == y && m % y == 0) ans2 = m;
            else ans2 = ans + (m / y) * y;
        } else ans2 = ans;
        if (ans2 < x) return ans2;
        return ans;
    }

    bool reachingPoints(int sx, int sy, int tx, int ty) {
        while (tx >= sx && ty >= sy) {
            if (sx == tx && sy == ty) return true;
            if (ty > tx) ty = pm(ty, tx, sy);
            else tx = pm(tx, ty, sx);
        }
        if (sx == tx && sy == ty) return true;
        return false;
    }
};
```
