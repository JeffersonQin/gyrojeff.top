---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-04-30 22:48:20'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-04-30 22:48:20'
parent: 0
password: ''
slug: 读论文YOLO-v1
status: publish
tags: [DL, ML, 机器学习, 深度学习]
template: ''
title: 读论文——YOLO v1
type: post
---
论文链接：https://arxiv.org/abs/1506.02640

## Abstract

* 以前都是用 Classifiers 来做 detection
* 现在：we frame object detection as a regression problem to spatially separated bounding boxes and associated class probabilities
* end-to-end
* FAST!
  * Fast YOLO: 155 fps, double mAP of other real-time detectors
  * YOLO: 45 fps

## Introduction

* Related works
  * Deformable Parts Models (DPM): sliding window approach where the classifier is run at evenly spaced locations over the entire image
  * R-CNN: 
    * first generate potential bounding boxes in an image
    * then run a classifier on these proposed boxes
    * post-processing: refine the bounding boxes, eliminate duplicate detections, and rescore the boxes based on other objects in the scene
* YOLO
  * Fast, realtime
  * simple architecture
  * see the entire image => less background error
  * learn generalizable representations of objects
  * accuracy 不行, 虽然识别率高，但是定位精准度相对低

## Architecture

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220416133949.png)

## Unified Detection

描述大致的识别思路。

1. 先划分成 $S\times S$ 方格，每个方格需要检测是否有物体的中心在这个方格内，产生 $B$ 个 bounding box
2. 每个 bounding box 会产生五个预测量 $x, y, w, h, \text{confidence}$。注意：$x,y$ 是相对于 cell 的，$w, h$ 是相对于整个图像的。
3. confidence 描述这个区域有物体的置信度。confidence 的定义：
  $$
      P(\text{Object}) \times \text{IoU}_{\text{pred}}^{\text{truth}}
  $$
4. 每个方格产生一个还会对 $C$ 个类别判断的概率，判断这个方格内是否含有类别 $C_i$
  $$
      P(\text{Class}_i \mid \text{Object})
  $$
5. 测试的时候每个 bounding box 的置信概率就是
  $$
      P(\text{Class}_i \mid \text{Object}) \times P(\text{Object}) \times \text{IoU}_{\text{pred}}^{\text{truth}} = P(\text{Class}_i) \times \text{IoU}_{\text{pred}}^{\text{truth}}
  $$

## Network Design

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220416113107.png)

我们取 $B=2$，每个区域生成两个 bounding box，区域数量 $S=7$，然后分类的类别是 $C=10$。所以最后的输出

$$
	S \times S \times (5B + C) = 7 \times 7 \times 30
$$

## Training

* Pretrain
  * ImageNet 数据集
  * 前 20 个卷积层（去掉最后四个卷积层和两个全连接层）再加上一个平均池化层和一个全连接层
* Activation
  * 出最后一个层外，使用 Leaky ReLU
    $$
        \phi(x) = \begin{cases}
            x & x > 0 \\ 0.1x & \text{otherwise}
        \end{cases}
    $$
  * 最后一层文章中说用的是 linear activation function
  * 除此之外，因为最后的结果应该在 $[0,1]$ 范围之内，查到一个 stackoverflow 的问题说最后可能对 output 逐元素做了 sigmoid. https://stackoverflow.com/questions/49707542/yolo-v1-bounding-boxes-during-training-step
* Hyper Parameters
  * $\text{batch size} = 64$
  * $\text{weight decay} = 0.0005$
  * $\text{momentum} = 0.9$
  * learning rate
    * $10^{-3} \rightarrow 10^{-2}$ for some epochs
    * $10^{-2}$ for $75$ epochs
    * $10^{-3}$ for $30$ epochs
    * $10^{-4}$ for $30$ epochs
* Regularization
  * Dropout (0.5) after the first connected layers

## Loss Function

### 问题一：均方差损失函数对所有东西的权重都相同

如果一个区域不含东西，那么根据定义 confidence 直接降到 0，但是这就会造成梯度的急剧抖动。同时，现在不管是对位置的预测还是对概率的置信，所有权重都是相同的，这显然不符合目标，所有作者提出了下面两个 param:

$$
	\lambda_{\text{coord}} = 5, \lambda_{\text{noobj}} = .5
$$

在算坐标预测的 loss 的时候提高权重，然后对于不含目标的划分区间降低权重。

### 问题二：不管 bounding box 的大小，权重都一样

解决方案：计算 $w, h$ 方根的均方损失

### 问题三

YOLO 对于每个区域都会产生若干个 bounding box，但是训练的时候我们只希望每个目标对应一个 bounding box。所以最后计算 loss 的时候，我们就会 assign 一个 bounding box predictor 给每个目标。这个 assign 的依据是取

$$
	\argmax_{i, B_i \in B} \text{IoU}_{\text{truth}}^{B_i}
$$

### Loss

原文好像 sigma 的下标有点问题，这里做一下修正

$$
	\begin{aligned}
		&\lambda_{\textbf{coord}} \sum_{i=1} ^ {S^2} \sum_{j=1}^{B} 1 _{ij}^{\text{obj}} \left[(x_i - \hat{x_i})^2 + (y_i - \hat{y_i})^2\right] \\ 
		+&\lambda_{\textbf{coord}} \sum_{i=1} ^ {S^2} \sum_{j=1}^{B} 1 _{ij}^{\text{obj}} \left[ \left(\sqrt{w_i} - \sqrt{\hat{w_i}}\right)^2 + \left(\sqrt{h_i} - \sqrt{\hat{h_i}}\right)^2 \right] \\ 
		+&\sum_{i=1} ^ {S^2} \sum_{j=1}^{B} 1 _{ij}^{\text{obj}}\left(C_i - \hat{C_i}\right)^2 \\ 
		+&\lambda_{\textbf{noobj}}\sum_{i=1} ^ {S^2} \sum_{j=1}^{B} 1 _{ij}^{\text{noobj}}\left(C_i - \hat{C_i}\right)^2 \\ 
		+&\sum_{i=1}^{S^2} 1_{i}^{\text{obj}} \sum_{c\in\text{classes}} (p_i(c) - \hat{p_i}(c))^2
	\end{aligned}
$$

where $1^{\text{obj}} _i$ denotes if object appears in cell $i$, and $1^{\text{obj}}_{ij}$ denotes that the $j$th bounding box predictor in cell $i$ is “responsible” for that prediction.

从上到下看还挺好理解的。

## 实践中的问题

对于每一个 cell，如果有多个东西就无解了，因为 loss 中没有说明这种情况。

## 一些想法

总的来说，Yolo v1 apply 了大量的 tricks... 太难训了。我自己训比原文低了 15 个点左右 ... 虽然用的是 resnet18/50。
