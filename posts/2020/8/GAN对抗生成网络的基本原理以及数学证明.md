---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [ML, 技术, 知识]
created: '2020-08-14 19:40:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/AM2.PNG',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-05-18 18:19:16'
parent: 0
password: ''
slug: '5'
status: publish
tags: [梯度上升, 梯度下降]
template: ''
title: GAN（对抗生成网络）的基本原理以及数学证明
type: post
---
# Preface

今天在PD Lib和DL斗智斗勇时，突然想起了自己非常想学的GAN，机缘巧合下便百度了，得到了以下两篇文章：

- https://zhuanlan.zhihu.com/p/72279816
- https://blog.csdn.net/jizhidexiaoming/article/details/96485095

于是便对GAN有了初步的了解（以前肯定是心不在焉才没有理解的（划掉）），随后又在五楼`生命科学`的书架上找到了相关资料，遂学了一波。

# GAN概述

> 2014 年，Ian Goodfellow 和他在蒙特利尔大学的同事发表了一篇震撼学界的论文。没错，我说的就是《Generative Adversarial Nets》，这标志着生成对抗网络（GAN）的诞生，而这是通过对计算图和博弈论的创新性结合。他们的研究展示，给定充分的建模能力，两个博弈模型能够通过简单的反向传播（backpropagation）来协同训练。
> 
> 这两个模型的角色定位十分鲜明。给定真实数据集 R，G 是生成器（generator），它的任务是生成能以假乱真的假数据；而 D 是判别器 （discriminator），它从真实数据集或者 G 那里获取数据， 然后做出判别真假的标记。Ian Goodfellow 的比喻是，G 就像一个赝品作坊，想要让做出来的东西尽可能接近真品，蒙混过关。而 D 就是文物鉴定专家，要能区分出真品和高仿（但在这个例子中，造假者 G 看不到原始数据，而只有 D 的鉴定结果——前者是在盲干）。
> 
> 理想情况下，D 和 G 都会随着不断训练，做得越来越好——直到 G 基本上成为了一个“赝品制造大师”，而 D 因无法正确区分两种数据分布输给 G。
> 
> 实践中，Ian Goodfellow 展示的这项技术在本质上是：G 能够对原始数据集进行一种无监督学习，找到以更低维度的方式（lower-dimensional manner）来表示数据的某种方法。而无监督学习之所以重要，就好像 Yann LeCun 的那句话：“无监督学习是蛋糕的糕体”。这句话中的蛋糕，指的是无数学者、开发者苦苦追寻的“真正的 AI”。
> ——[pytorch实现GAN](https://blog.csdn.net/jizhidexiaoming/article/details/96485095)

GAN - Generative Adversarial Nets, 生成对抗网络，简单来讲其有两个组成部分：

- D (Discriminator) - 判别器，判断输入时捏造的还是真实的
- G (Generator) - 生成器，从随机噪声中生成我们想要的数据

随着训练的进行，我们要提高D的辨析能力，但同时也要G的能力来骗过D，因为我们的最终目的是要让G来生成可以骗过D的信息。总结来说，通过对这两个模型的训练，我们就可以找到随机噪声与有意义数据的映射，达到`创作`的目的。

# GAN的流程和目标函数

## GAN的目标函数

GAN的目标函数如下：

$$
V(D, G) = \mathbb E_{x \sim P_{\text{data}} } [\log D(x)] + \mathbb E_{\boldsymbol {z} \sim P_z}[\log(1 - D(G(\boldsymbol {z})))]
$$

其中，$D$为`Discriminator`的模型函数，$G$为`Generator`的模型函数，随机变量$x$服从原来正确的数据集的分布$P_\text {data}$，随机变量（这里可能是高维随机变量，取决于模型具体实现）$\boldsymbol {z}$服从分布$P_z$（生成噪音），$\mathbb E$代表期望。

## GAN的流程

$$
G^* = \arg \min _G \max _D V(D, G)
$$

即，可以分为两步理解：

1. 在$G$为常数的情况下，选择合适的$D$使得$V(D,G)$能够最大化。
2. 在这之后，选取合适的$G$来最小化$V(D, G)$，这个$G$就是我们想要的生成模型。

在每一步的训练中：

- 取$m$个真实数据：
  
  $$
  \{x^{(1)}, x^{(2)}, x^{(3)}, \cdots, x^{(m)}\}
  $$
  
  使用$G$和$m$组随机数（服从于噪音分布$P_G$，一般使用服从正态分布的随机数）
  
  $$
  \{\boldsymbol {z}^{(1)}, \boldsymbol {z}^{(2)}, \boldsymbol {z}^{(3)}, \cdots, \boldsymbol {z}^{(m)}\}
  $$
  
  生成$m$个假数据，其中
  
  $$
  \forall i \in [1, m], i \in \mathbb Z \Rightarrow x^{(i)} \sim P_{\text{data}}, \boldsymbol {z} ^ {(i)} \sim P_z
  $$
- 根据$\max$部分的目标使用随机梯度上升（Stochastic Gradient Ascent）更新$D$的参数，提高$D$的分辨能力
  
  $$
  \theta_d := \theta_d + \alpha_d \nabla_{\theta_d} \frac 1 m \sum _ {i = 1} ^ m \Big [ \log D\big ( x^{(i)} \big) + \log \big ( 1 - D \big ( G(\boldsymbol{z}^{(i)}) \big ) \big ) \Big]
  $$
- 根据$\min$部分的目标使用随机梯度下降（Stochastic Gradient Descent）更新$G$的参数，使$G$生成的数据更有迷惑性
  
  $$
  \theta_g := \theta_g - \alpha_g\nabla_{\theta_g} \frac 1 m \sum _{i = 1} ^ m\log \Big ( 1 - D\big ( G(\boldsymbol{z} ^ {(i)})\big ) \Big )
  $$

# GAN的数学原理

## Prerequisites

### 信息量(自信息)

信息量是指信息多少的量度，即，对于一条信息，传达这条信息所需的最少信息长度为自信息。

信息论创始人C.E.Shannon，1938年首次使用比特（bit）概念：1（bit）= $\log_2 2$。它相当于对二个可能结局所作的一次选择量。信息论采用对随机分布概率取对数的办法，解决了不定度的度量问题。

定义：符合分布$P$的某一事件$x$出现，传达出这条信息的信息量记为：

$$
I = \log \frac 1 {P(x)} = - \log P(x)
$$

### 香农熵

从离散分布$P$中随机抽选一个事件，传达这条信息所需的最优平均信息长度为香农熵，表达为：

$$
H(P) = \sum_x P(x) \log \frac 1 {P(x)} = - \sum_x P(x) \log P(x)
$$

若分布是连续的，则：

$$
H(P) = \int_x P(x) \log \frac 1 {P(x)} \mathrm dx = -\int_x P(x) \log P(x) \mathrm dx
$$

### 交叉熵

用分布$P$的最佳信息传递方式来传达分布$Q$中随机抽选的一个事件，所需的平均信息长度为交叉熵，表达为

$$
H_P(Q) = \sum_x Q(x) \log \frac 1 {P(x)} = - \sum_x Q(x) \log P(x)
$$

$$
H_P(Q) = \int_x Q(x) \log \frac 1 {P(x)} \mathrm dx = - \int_x Q(x) \log P(x) \mathrm dx
$$

### $KL$ Divergence

$KL$散度：用分布$P$的最佳信息传递方式来传达分布$Q$，比用分布$Q$自己的最佳信息传递方式来传达分布$Q$，平均多耗费的信息长度为$KL$散度，表达为$D_P(Q)$或$D_{KL}(Q||P)$，$KL$散度衡量了两个分布之间的差异。

$$
\begin{aligned}
        D_{KL}(Q||P) = D_P(Q) &= H_P(Q) - H(Q) \\
        &= \sum_x Q(x) \log \frac 1 {P(x)} - \sum _x Q(x) \log \frac 1 {Q(x)} \\
        &= \sum_x Q(x) \log \frac {Q(x)} {P(x)}
    \end{aligned}
$$

对于连续分布：

$$
D_{KL}(Q||P) = D_P(Q) = \int_{-\infty} ^ {\infty} P(x)\log \frac {P(x)}{Q(x)} \mathrm dx
$$

KL Divergence越大，两个分布差异越大，反之差异越小。

## 数学原理

看完Prerequisites，我们回归正题讨论GAN的原理。我们现在想要做的事情，其实就是将一个服从$P_G$的随机噪声$\boldsymbol z$通过一个生成网络$G$得到一个和真实数据分布$P_{\text {data}}(x)$差不多的生成分布$P_G(x;\theta_g)$，其中$\theta_g$为生成网络$G$的参数。我们希望找到一个$\theta_g$使得两个分布$P_{\text {data}}(x)$与$P_G(x;\theta)$尽可能地相似（使得他们地KL散度尽可能得小）。

我们从真实数据分布$P_\text{data}(x)$中取$m$个样本，记作：

$$
\{x^{(1)}, x^{(2)}, x^{(3)}, \cdots, x^{(m)}\}
$$

根据生成网络的参数$\theta_g$，我们可以计算出这$m$个真实样本在生成网络中出现的概率$P_G(x^{(i)}; \theta_g)$，那么生成这样的$m$个样本数据的似然（likelihood）为：

$$
L = \prod_{i = 1} ^ m P_G(x^{(i)}; \theta_g)
$$

由于我们想要两个分布尽量相似，那么我们肯定希望这个似然$L$尽量大，即生成这样的真实数据的概率尽量大，遂我们最大化这个似然，找到$\theta_g^*$：

$$
\begin{aligned}
        \theta_g^* &= \arg \max _ {\theta_g} \prod _ {i = 1} ^ m P_G(x^{(i)}; \theta_g) \\
        &\Leftrightarrow \arg \max _ {\theta_g} \log \prod _ {i = 1} ^ m P_G(x^{(i)}; \theta_g) \\
        &= \arg \max _ {\theta_g} \sum_{i = 1} ^ m \log P_G(x^{(i)}; \theta_g) \\
        &\approx \arg \max _ {\theta_g} \mathbb E_{x \sim P_{\text {data}}} [\log P_G(x; \theta_g)] \\
        &= \arg \max _ {\theta_g} \int _ x P_\text{data}(x) \log P_G(x; \theta_g)\mathrm dx \\
        &= \arg \max _ {\theta_g} \int _ x P_\text{data}(x) \log P_G(x; \theta_g)\mathrm dx \\&~~~~- \int _ x P_\text{data}(x) \log P_\text {data}(x)\mathrm dx \\
        &= \arg \max _ {\theta_g} \int _ x P_\text {data} (x) \log \frac {P_G(x; \theta_g)} {P_\text {data}(x)} \mathrm dx \\ 
        &= \arg \max _ {\theta_g} - \int _ x P_\text {data} (x) \log \frac {P_\text {data}(x)} {P_G(x; \theta_g)} \mathrm dx \\
        &= \arg \min _ {\theta_g} KL(P_\text {data} || P_G(x; \theta))
    \end{aligned}
$$

所以可见，其实最大化这个似然，和最小化KL散度是基本相同的。

上述式子中，$P_G(x;\theta_g)$代表在生成分布中出现$x$的概率，也可以如下计算得到：

$$
P_G(x) = \int _ \boldsymbol{z} P_z(z)\cdot 1\{G(z) = x\} \mathrm dz
$$

注：$1\{\cdot\}$的含义是若打括号内的逻辑运算为真则取$1$，假则取$0$. 即

$$
1\{\text {True}\} = 1, 1 \{\text {False}\} = 0
$$

但是我们发现，上述的过程是难以进行计算的，甚至完全没办法求$P_G(x)$，这只是模型的想法而已。

现在我们看回之前我们提到的目标函数：

$$
V(D, G) = \mathbb E_{x \sim P_{\text{data}} } [\log D(x)] + \mathbb E_{\boldsymbol {z} \sim P_z}[\log(1 - D(G(\boldsymbol {z})))]
$$

与最优化生成模型：

$$
G^* = \arg \min _G \max _D V(D, G)
$$

我们接下来分步解释。

首先，我们不妨解释一下$\max_D V(G, D)$，这部分的含义之前也解释过，是在给定$G$的情况下，最大化$V(G, D)$。观察发现，其形式其实与交叉熵损失函数非常相似：

$$
\mathcal L(\hat y, y) = -(y\log \hat y + (1 - y) \log (1 - \hat y))
$$

其实他们表达的目的也差不多。我们先化简一下$V(G, D)$看看能得到什么结果：

$$
\begin{aligned}
            V(G, D) &= \mathbb E_{x \sim P_{\text{data}} } [\log D(x)] + \mathbb E_{\boldsymbol {z} \sim P_z}[\log(1 - D(G(\boldsymbol {z})))] \\
            &= \int_x P_\text {data}(x) \log D(x) \mathrm dx + \int_\boldsymbol{z} P_z(\boldsymbol{z}) \log (1 - D(G(\boldsymbol{z})))\mathrm dz \\
            &= \int_x P_\text {data}(x) \log D(x) \mathrm dx + \int_x P_G(x)\log (1 - D(x)) \mathrm dx \\
            &= \int_x [P_\text {data}(x) \log D(x) + P_G(x)\log (1 - D(x))] \mathrm dx
    \end{aligned}
$$

让我们考察积分内部的项，我们可以对它做指数运算，即：

$$
e^{P_\text {data}(x) \log D(x) + P_G(x)\log (1 - D(x))} = D(x) ^ {P_\text {data}}\times(1- D(x))^{P_G(x)}
$$

其想表达什么便不言而喻了，它表达的就是判别器判别是真的的正确率和判别是假的的正确率，总体来说就是衡量$D$的能力，所以我们想要最大化$V$，提高$D$的判别能力。

令

$$
f(D) = P_\text {data}(x) \log D(x) \mathrm dx + P_G(x)\log (1 - D(x))
$$

因为这里$P_\text {data} (x)$和$P_G(x)$都可以看作常数，所以

$$
\arg \max _ D \int_x f(D) \mathrm dx = \arg \max _D f(D)
$$

最大化$f(D)$，即令其导数为$0$：

$$
\frac {\mathrm d f(D)} {\mathrm dD } = \frac {P_\text {data}(x)}{D} - \frac {P_G(x)} {1 - D} = 0
$$

则：

$$
\frac {P_\text {data}(x)}{D} = \frac {P_G(x)} {1 - D}
$$

$$
D^*(x) = \frac {P_\text {data}(x)}{P_\text {data}(x) + P_G(x)}
$$

这样，我们就得到了那个状态下最优的$D^*$的表达式。我们将这个能够最大化$V$的$D$代入回$V(G, D)$：

$$
\begin{aligned}
        \max V(G, D) &= V(G, D^*) \\
        &= \mathbb E_{x \sim P_\text {data}} \Big [ \log \frac {P_\text {data}(x)}{P_\text {data}(x) + P_G(x)} \Big] + \mathbb E_{x \sim P_G} \Big [ \frac {P_G(x)}{P_\text {data}(x) + P_G(x)}\Big ] \\
        &= \int_x P_\text {data} (x) \log \frac {\frac 1 2 P_\text {data}}{\frac 1 2 (P_\text{data}(x) + P_G(x))} \mathrm dx + \int _ x P_G(x) \log \frac {\frac 1 2 P_G(x)} {\frac 1 2 (P_\text {data} (x) + P_G(x))} \mathrm dx \\
        &= -2\log 2 + D_{KL} (P_\text {data} || \frac 1 2 [P_\text{data}(x) + P_G(x)]) + D_{KL} (P_G(x) || \frac 1 2 [P_\text {data}(x) + P_G(x)]) \\
        &= -2\log 2 + JSD (P_\text{data}(x) || P_G(x))
    \end{aligned}
$$

其中，我们引入了$JS$ Divergence，定义如下：

$$
JSD(P||Q) = \frac 1 2 D_{KL} (P||M) + \frac 1 2 D_{KL} (Q || M), M = \frac 1 2 (P + Q)
$$

容易得到，KL Divergence是不对称的，而JS Divergence是对称的。他们都可以衡量两组分布建的差异。这里我们想要两组分布差异最小，故取$\min$

所以，这也就解释了为什么：

$$
\arg \min _G \max _D V(G, D)
$$

是我们的目标过程。

