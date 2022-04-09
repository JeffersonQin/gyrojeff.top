---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-04-07 22:16:08'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-04-07 22:16:08'
parent: 0
password: ''
slug: leetcode-1787-使所有区间的异或结果为零-DP-随机跳题计划
status: publish
tags: [DP, OI]
template: ''
title: leetcode 1787 使所有区间的异或结果为零 - DP - 随机跳题计划
type: post
---
## Preface

好耶，少见的难题。

## 题面

https://leetcode-cn.com/problems/make-the-xor-of-all-segments-equal-to-zero/

## 题解

大部分题解已经提到了：就是下面这个结论。

结论：最终构造出来的数列以 $k$ 为一个周期

证明：

由题设：

$$
	a[1] \oplus a[2] \oplus \cdots \oplus a[k] = a[2] \oplus a[3] \oplus \cdots \oplus a[k + 1]
$$

所以我们有：

$$
	(a[1] \oplus a[2] \oplus \cdots \oplus a[k]) \oplus (a[2] \oplus a[3] \oplus \cdots \oplus a[k + 1]) = 0
$$

根据异或的交换律与结合律：

$$
	a[1] \oplus a[k + 1] = 0
$$

故 $a[1] = a[k + 1]$。易证，此结论可以将 $1$ 替换为任意定义域内的数。

接下来思考一下最终构造出来的数列满足什么特征：

1. 以 $k$ 为一个周期
2. 第一个周期异或和为 0

接下来看图说话:

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220407222251.png)

在上图中 $n = 13, k = 3$，我们就是需要让

$$
	a[1] = a[4] = a[7] = a[10] = a[13]
$$

$$
	a[2] = a[5] = a[8] = a[11]
$$

$$
	a[3] = a[6] = a[9] = a[12]
$$

$$
	a[1] \oplus a[2] \oplus a[3] = 0
$$

那么我们不妨一组一组按列处理，接下来是状态的设法：$f[i][j]$ 代表处理完第 $i$ 列，使得前 $i$ 列的异或和为 $j$ 的最少修改次数。

思考转移方程：都是从上一列转移，但是有两种转移方式：

1. 第 $i$ 列全部修改
2. 第 $i$ 列部分修改，部分保留

设 $cnt$ 为第 $i$ 列的个数，$t[j]$ 代表第 $i$ 列 $j$ 出现的次数，那么：

$$
	f[i][j] = \min (f[i][j], cnt + \min_{k} f[i - 1][k])
$$

$$
	f[i][j] = \min_{k \in \text{column }i} \{f[i][j], f[i - 1][j \oplus k] + cnt - t[k]\}
$$

注意：全部修改仍然有可能比部分修改更优，这是因为本题不是简单的贪心。具体可以见下一小节，我们可以构造出反例，证明贪心是错误的。

此外，我们还需要注意初值的设定。

## 构造贪心的反例

贪心？怎么贪心？那当然是每一列选择相同数量最多的，再把这个数量排序，相同数量最少的一列全部改变，使得最后异或值为 0。

但是这个反例是非常容易构造的。

第一列：

$$
	a, a, b, b, b, c, c
$$

第二列：

$$
	d, d, d, d, e, e, e
$$

第三列：

$$
	f,f,g,g,h,h,i
$$

按照之前说的贪心，我们会选保留 $b, d$，然后第三列适应答案使得异或为 $0$。但是如果很不巧，我们的构造在这样的情况下其他元素全部需要改变，那么此时的改动数量为 $4 + 5 + 7 = 16$。然而如果 $a \oplus e \oplus f = 0$，那么如果我们选择 $a, e, f$，改动的数量就是 $5 + 4 + 5 = 14 < 16$，更优。构造完毕。

## 代码

```c++
const int maxn = 2e3+5;
const int maxl = 10;

class Solution {

int f[maxn][1 << maxl];
int g[maxn], a[maxn], t[1 << maxl];

public:
    int minChanges(vector<int>& nums, int k) {
        int n = nums.size();
        for (int i = 0; i < n; i ++) a[i + 1] = nums[i];
        memset(f, 0x7f, sizeof(f));
        memset(g, 0x7f, sizeof(g));
        
        memset(t, 0, sizeof(t));
        int cnt = 0;
        for (int j = 1; j <= n; j += k)
            t[a[j]] ++, cnt ++;
        for (int j = 0; j < 1024; j ++) {
            f[1][j] = min(f[1][j], cnt - t[j]);
            g[1] = min(g[1], f[1][j]);
        }

        for (int i = 2; i <= k; i ++) {
            memset(t, 0, sizeof(t));
            cnt = 0;
            for (int j = i; j <= n; j += k)
                t[a[j]] ++, cnt ++;
            for (int j = 0; j < 1024; j ++) {
                f[i][j] = g[i - 1] + cnt;
                for (int m = 0; m < cnt; m ++) {
                    f[i][j] = min(f[i][j], f[i - 1][j ^ a[m * k + i]] + cnt - t[a[m * k + i]]);
                }
                g[i] = min(g[i], f[i][j]);
            }
        }
        return f[k][0];
    }
};
```
