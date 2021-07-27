---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [记录]
created: '2020-11-19 19:24:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/24.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:42'
parent: 0
password: ''
slug: '56'
status: publish
tags: [Matplotlib, Python, 化学, 绘图]
template: ''
title: 化学老师打工人 | 用Matplotlib绘制三维图
type: post
---
# Preface

化学老师学校里要开课，遂给了我二十五个元素信息，让我整张看起来牛逼的元素周期表。

# 思路

- 数据分析
- 两两维度寻找相关性（较为失败）
- 找出三个维度进行绘图（直接瞎搞）

# 结果

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/11/1605843220.png)

# 实现

```python
import matplotlib.pyplot as plt
import numpy as np
from mpl_toolkits.mplot3d import Axes3D  # 空间三维画图


fig = plt.figure()
ax = Axes3D(fig)
ax.bar3d(X, Y, bottom, width, height, Z, shade=True)
ax.set_xlabel('X Label')
ax.set_ylabel('Y Label')
ax.set_zlabel('Z Label')
```

