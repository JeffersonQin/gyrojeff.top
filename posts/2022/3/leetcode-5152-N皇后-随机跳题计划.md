---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-28 11:20:54'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-28 11:20:54'
parent: 0
password: ''
slug: leetcode-5152-N皇后-随机跳题计划
status: publish
tags: [DFS, OI]
template: ''
title: leetcode 51,52 N皇后 - 随机跳题计划
type: post
---
## 题面

* https://leetcode-cn.com/problems/n-queens/
* https://leetcode-cn.com/problems/n-queens-ii/

## 题解

首先不考虑斜向，根据全排列的性质，1 ~ n 的全排列，以序数为行号，数值为列号就能穷举出不斜向攻击下的 N 皇后摆放情况。所以我们只需要穷举全排列，然后判断每个排列是否可行，复杂度为 $O(n!)$

## 代码

51:

```c++
// https://leetcode-cn.com/problems/n-queens/
const int maxn = 15;

class Solution {
public:

    bool chosen[maxn];
    int res[maxn];

    vector<vector<string>> ans;

    string get_string(int pos, int n) {
        string s = "";
        for (int i = 1; i < pos; i ++)
            s += ".";
        s += "Q";
        for (int i = pos + 1; i <= n; i ++)
            s += ".";
        return s;
    }

    void next_permutation(int x, int n) {
        if (x == n + 1) {
            // judge
            for (int i = 1; i < n; i ++)
                for (int j = i + 1; j <= n; j ++)
                    if (res[i] + j - i == res[j] || res[i] + i - j == res[j])
                        return;
            // output
            vector<string> ret = vector<string>();
            for (int i = 1; i <= n; i ++) 
                ret.push_back(get_string(res[i], n));
            ans.push_back(ret);
        }
        for (int i = 1; i <= n; i ++) {
            if (chosen[i]) continue;
            chosen[i] = true;
            res[x] = i;
            next_permutation(x + 1, n);
            chosen[i] = false;
        }
    }

    vector<vector<string>> solveNQueens(int n) {
        next_permutation(1, n);
        return ans;
    }
};
```

52:

```c++
// https://leetcode-cn.com/problems/n-queens-ii/
const int maxn = 15;

class Solution {
public:

    bool chosen[maxn];
    int res[maxn];

    int ans = 0;

    void next_permutation(int x, int n) {
        if (x == n + 1) {
            // judge
            for (int i = 1; i < n; i ++)
                for (int j = i + 1; j <= n; j ++)
                    if (res[i] + j - i == res[j] || res[i] + i - j == res[j])
                        return;
            ans ++;
        }
        for (int i = 1; i <= n; i ++) {
            if (chosen[i]) continue;
            chosen[i] = true;
            res[x] = i;
            next_permutation(x + 1, n);
            chosen[i] = false;
        }
    }

    int totalNQueens(int n) {
        next_permutation(1, n);
        return ans;
    }
};
```
