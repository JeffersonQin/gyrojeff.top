---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-28 12:08:52'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-28 12:08:52'
parent: 0
password: ''
slug: leetcode-960-删列造序III-随机跳题计划
status: publish
tags: [DP, OI]
template: ''
title: leetcode 960 删列造序 III - 随机跳题计划
type: post
---
## Preface

刷 leetcode 好有成就感，毕竟在其他 OJ 直接被虐爆。

## 题面

https://leetcode-cn.com/problems/delete-columns-to-make-sorted-iii/

## 题解

这不就是最长递增子序列吗...只不过现在多了点字符串。很容易可以得到转移方程：

$$
	f[i] = \max_{j < i, \forall s \Rightarrow s[j] \leq s[i]} f[j] + 1
$$

其中 $f[i]$ 代表以 $i$ 为结尾的时候的最长递增子序列的长度。

$$
	ans = s.length - \max f
$$

## 代码

```c++
const int maxn = 105;

class Solution {

int f[maxn];

public:
    int minDeletionSize(vector<string>& strs) {
        int n = strs.size();
        int m = strs[0].size();
        f[0] = 1;
        int ans = m - 1;
        for (int i = 1; i < m; i ++) {
            f[i] = 1;
            for (int j = 0; j < i; j ++) {
                bool cap = true;
                for (int p = 0; p < n; p ++)
                    if (strs[p][j] > strs[p][i])
                        cap = false;
                if (!cap) continue;
                f[i] = max(f[i], f[j] + 1);
            }
            ans = min(ans, m - f[i]);
        }
        return ans;
    }
};
```
