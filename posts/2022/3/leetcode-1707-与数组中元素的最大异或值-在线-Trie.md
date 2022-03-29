---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-29 15:06:48'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-29 15:06:48'
parent: 0
password: ''
slug: leetcode-1707-与数组中元素的最大异或值-在线-Trie
status: publish
tags: [OI, Trie]
template: ''
title: leetcode 1707 与数组中元素的最大异或值 - 在线 Trie
type: post
---
## Preface

吐了。写的时候想假了，以为可以用最高位判断，最后发现还不如存个 min 数组。不过问题不大，正好复习了一下 Trie 的写法（翻了翻以前 AC 自动机的写法）。以及，初始化 `sz = 1`，血的教训。

## 题面

https://leetcode-cn.com/problems/maximum-xor-with-an-element-from-array/

## 题解

观察数据范围，要到 1e5，说明基本上就是 $O(n\log n), O(n\log^2 n)$ 这种。根据异或运算的性质，将每个数存成二进制 Trie 是一个很好的选择。

接下来考虑题目的附加条件：那个啥的最大值。问题不大，给每个节点存一个该子树下的最小值，这样在遍历的时候就不用回溯了。拿空间换时间。

再来说一下遍历。从高位往低位遍历，尽可能取异或运算数当前位的反，因为：

* `1 ^ 0 = 0 ^ 1 = 1`
* `1 ^ 1 = 0 ^ 0 = 0`

leetcode 官方的这两张图不错，省得我自己画了：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220329190636.png)

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220329190621.png)

## 代码

```c++
const int maxn = 2e6+5;

class Solution {

int child[maxn][2];
int d[31];
int m[maxn];
bool ch[2];

int sz = 1;

void add(int n) {
    int p = 1;
    m[p] = min(m[p], n);
    for (int i = 30; i >= 0; i --) {
        int b = !!(n & (1LL << i));
        if (!child[p][b])
            child[p][b] = ++ sz;
        p = child[p][b];
        m[p] = min(m[p], n);
    }
}

public:
    vector<int> maximizeXor(vector<int>& nums, vector<vector<int>>& queries) {
        memset(m, 0x7f, sizeof(m));
        vector<int> a = vector<int>();
        for (int i = 0; i < nums.size(); i ++)
            add(nums[i]);
        for (int i = 0; i < queries.size(); i ++) {
            int x = queries[i][0], mm = queries[i][1];
            int p = 1;
            bool ans = true;

            if (m[p] > mm) ans = false;
            else
                for (int i = 30; i >= 0; i --) {
                    int xbc = !(x & (1LL << i));
                    int choose = 0;
                    ch[0] = !!child[p][0]; ch[1] = !!child[p][1];
                    if (m[child[p][1]] > mm) ch[1] = false;
                    if (m[child[p][0]] > mm) ch[0] = false;
                    if (ch[xbc]) {
                        choose = xbc;
                    } else if (ch[xbc ^ 1]) {
                        choose = xbc ^ 1;
                    } else {
                        choose = -1;
                        ans = false;
                        break;
                    }
                    p = child[p][choose];
                    d[i] = choose;
                }
            if (ans) {
                int b = 0;
                for (int i = 30; i >= 0; i --) {
                    b <<= 1LL; b += d[i];
                }
                a.push_back(b ^ x);
            } else {
                a.push_back(-1);
            }
        }
        return a;
    }
};
```
