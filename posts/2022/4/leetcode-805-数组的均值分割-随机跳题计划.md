---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-04-05 00:12:55'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-04-05 00:12:55'
parent: 0
password: ''
slug: leetcode-805-数组的均值分割-随机跳题计划
status: publish
tags: [DP, OI]
template: ''
title: leetcode 805 数组的均值分割 - 随机跳题计划
type: post
---
## Preface

被录取的喜悦冲昏了头脑，停了几天跳题，今天又开始跳了。

## 题面

https://leetcode-cn.com/problems/split-array-with-same-average/

## 题解

一开始做的时候想假了，一眼就觉得是普通的子集和，就是说看能不能找到一个子集使得和为一个定值。写了一发才发现这题目说的是均值。

但是问题不大，因为 $n$ 很小，所以我们可以枚举所有的自己大小，然后魔改一下子集和这个 01 背包。

其实写到一半发现写假了不想做了，但是一看题解让我大失所望。那些提到 01 背包的竟然复杂度要上指数级？这显然不能接受。

接下来讲一下思路：

首先讲一下均值的处理问题。算除法显然不是一个好的选择，所以这里我们用乘法代替。设 $x, y$ 为整个和的两个部分：

$$
	x + y = \text{sum} = \sum_i a[i]
$$

满足下面的条件：

$$
	\frac {x} {y} = \frac {|x|} {|y|}
$$

$$
	|x| + |y| = n
$$

其中 $|x|, |y|$ 代表 $x, y$ 代表的子集的大小。这里我们不妨枚举 $|x|$，记作 $m$，那么

$$
	y = \frac {\text {sum} \times (n - m)} {n}
$$

我们只需要判断有没有可能通过选择 $n-m$ 个数使得和为 $y$ 也就是上式。

接下来讲一下魔改的子集和。

常规的子集和：设 $f[i]$ 代表 $i$ 能否成为一个子集和：

$$
	f[i] = f[i] \text { or } f[i - a[j]]
$$

$$
	f[0] = 1
$$

从后往前更新 $i$。这个是子集和的惯用套路。

这里我们定义 $c[i][t]$，意为 $i$ 能否用 $t$ 个数表示，作为一个子集和。

$$
	c[i][t] = c[i][t] \text { or } c[i - a[j]][t - 1]
$$

$$
	c[0][0] = 1
$$

这里需要注意一下更新条件：$f[i - a[j]] = 1$ 才能更新 $c[i][t]$，理由无需多说。

## 代码

```c++
const int maxn = 150005;

class Solution {

int sum = 0;
bool f[maxn], c[maxn][31];

public:
    bool judgeSubset(vector<int>& nums, int n, int s) {
        memset(f, 0, sizeof(f));
        memset(c, 0, sizeof(c));
        f[0] = 1;
        c[0][0] = 1;
        for (int i = 0; i < nums.size(); i ++)
            for (int j = s; j >= nums[i]; j --) {
                f[j] = f[j] || f[j - nums[i]];
                if (f[j - nums[i]])
                    for (int t = 30; t > 0; t --)
                        c[j][t] = c[j - nums[i]][t - 1] || c[j][t];
            }
        return c[s][n];
    }

    bool d(int n, int d) {
        return !(n % d);
    }

    bool splitArraySameAverage(vector<int>& nums) {
        int n = nums.size();
        for (int i = 0; i < n; i ++) sum += nums[i];
        for (int m = 1; m < n; m ++) {
            if (!d((n - m) * sum , n)) continue;
            if (judgeSubset(nums, n - m, (n - m) * sum / n)) return true;
        }
        return false;
    }
};
```
