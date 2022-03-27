---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-24 18:55:23'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-24 18:55:23'
parent: 0
password: ''
slug: leetcode-1027-随机跳题计划
status: publish
tags: [DP, OI]
template: ''
title: leetcode 1027 随机跳题计划
type: post
---
## Preface

随机跳题。

我现在真的暴怒。`leetcode` 这勾八 OJ 每组数据每次不会重新编译，而是会反复调用一个方法 😅。导致我的全局数组从来不清零。😅

## 题面

https://leetcode-cn.com/problems/longest-arithmetic-subsequence/

## 题解

先推个朴素的方程。设 $f[i][d]$ 为以 $i$ 为结尾，以 $d$ 为公差的数列的最长长度：

$$
	f[i][d] = \max_{a[j] =a[i] - d} f[j][d] + 1
$$

可以发现这个复杂度是 $O(n^2d)$，太大了，稍做优化，令 $d = a[i] - a[j]$，我们就可以顺序更新。

$$
	f[i][d] = \max {f[i][d], f[j][d] + 1}, d = a[i] - a[j]
$$

还有一些细节，比方说 d 的范围，我们可以做一个线性变换，把负的映射成正的。

## 代码

```c++
const int maxn = 1e3+5;

class Solution {

int f[maxn][maxn], n, a[maxn];

public:
    int longestArithSeqLength(vector<int>& nums) {
        n = nums.size();
        for (int i = 0; i < n; i ++) a[i + 1] = nums[i];
        int ans = 0;
        for (int i = 1; i <= n; i ++)
            for (int j = 1; j < i; j ++) {
                int d = a[i] - a[j] + 500;
                f[i][d] = max(f[i][d], f[j][d] + 1);
                ans = max(ans, f[i][d]);
            }
        return ans + 1;
    }
};
```
