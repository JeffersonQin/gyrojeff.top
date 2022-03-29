---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: []
created: '2022-03-28 19:59:42'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-28 23:03:12'
parent: 0
password: ''
slug: 从精度问题看-softmax-与-CrossEntropyLoss
status: publish
tags: []
template: ''
title: 从精度问题看 softmax 与 CrossEntropyLoss
type: post
---
## Softmax 运算

softmax 运算用于将实数序列映射为和为一的概率序列，换句话说，softmax 运算用于将实数值规格化，使其和为一，映射成一个合理的概率分布。

下面是 softmax 的定义：

$$
	\hat{\mathbf{y}} = \mathrm{softmax}(\mathbf{o})\quad \text{where}\quad \hat{y}_j = \frac{\exp(o_j)}{\sum_k \exp(o_k)}
$$

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/softmaxreg.svg)

## 对数似然与交叉熵损失函数

这里应用 d2l 教科书上的一系列公式与语段：

softmax函数给出了一个向量 $\hat{\mathbf{y}}$， 我们可以将其视为“对给定任意输入$\mathbf{x}$的每个类的条件概率”。 例如，$P(y=\text{猫} \mid \mathbf{x})$。 假设整个数据集 $\{\mathbf{X}, \mathbf{Y}\}$ 具有 $n$ 个样本， 其中索引 $i$ 的样本由特征向量 $\mathbf{x}^{(i)}$ 和独热标签向量 $\mathbf{y}^{(i)}$ 组成。 我们可以将估计值与实际值进行比较：

$$
	P(\mathbf{Y} \mid \mathbf{X}) = \prod_{i=1}^n P(\mathbf{y}^{(i)} \mid \mathbf{x}^{(i)})
$$

根据最大似然估计，我们最大化 $P(\mathbf{Y} \mid \mathbf{X})$ ，相当于最小化负对数似然：

$$
	-\log P(\mathbf{Y} \mid \mathbf{X}) = \sum_{i=1}^n -\log P(\mathbf{y}^{(i)} \mid \mathbf{x}^{(i)})
= \sum_{i=1}^n l(\mathbf{y}^{(i)}, \hat{\mathbf{y}}^{(i)})
$$

其中，对于任何标签 $\mathbf{y}$ 和模型预测 $\hat{\mathbf{y}}$ ，损失函数为：

$$
	l(\mathbf{y}, \hat{\mathbf{y}}) = - \sum_{j=1}^q y_j \log \hat{y}_j
$$

## 再论交叉熵损失函数

我们一定要明确一个点：交叉熵损失函数的值是一个数。我们再来看这个公式：

$$
	l(\mathbf{y}, \hat{\mathbf{y}}) = - \sum_{j=1}^q y_j \log \hat{y}_j
$$

* $\mathbf{y}$ 是一个独热编码的向量
* $\hat{\mathbf{y}}$ 是经过 softmax 计算的各分量和为一的预测向量
* 对于所有的 $y_j$，只有一个值为 $1$，其余皆为 $0$.

## 交叉熵损失函数的实现

```python
# y_hat: [vector] -> matrix
# y: [scalar, represent index] -> vector
# the outer dimension represent sample count
def cross_entropy(y_hat, y):
    # 这里的 range(len(y_hat)) 的意思是对于每一个 sample 都做计算，是一个 index 集合
    return - torch.log(y_hat[range(len(y_hat)), y])

y_hat = torch.tensor(
	[[0.2, 0.7, 0.1], 
	[0.2, 0.7, 0.1], 
	[0.4, 0.6, 0]])
y = [1, 1, 1]
cross_entropy(y_hat, y)
```

```
Out: tensor([0.3567, 0.3567, 0.5108])
```

由于是批量计算，所以这里 `y_hat` 是矩阵，`y` 是向量，返回值也是一个向量。

## Softmax 带来的问题

https://zh-v2.d2l.ai/chapter_linear-networks/softmax-regression-concise.html#subsec-softmax-implementation-revisited

简而言之，就是 softmax 既有可能带来指数所造成的数值过大的问题，也有可能带来除法所造成的精度丧失的问题。

为了解决第一个问题，我们可以尝试通过在计算 softmax 之前从每个参数 $o_k$ 中减去 $\max(o_k)$ :

$$
	\begin{aligned}
		\hat y_j & =  \frac{\exp(o_j - \max(o_k))\exp(\max(o_k))}{\sum_k \exp(o_k - \max(o_k))\exp(\max(o_k))} \\
		& = \frac{\exp(o_j - \max(o_k))}{\sum_k \exp(o_k - \max(o_k))}
	\end{aligned}
$$

为了解决第二个问题，我们可以将 softmax 与交叉熵损失函数结合起来一起计算。因为交叉熵损失函数带了一个对数运算，可以与指数运算相抵消：

$$
	\begin{aligned}
	\log{(\hat y_j)} & = \log\left( \frac{\exp(o_j - \max(o_k))}{\sum_k \exp(o_k - \max(o_k))}\right) \\
	& = \log{(\exp(o_j - \max(o_k)))}-\log{\left( \sum_k \exp(o_k - \max(o_k)) \right)} \\
	& = o_j - \max(o_k) -\log{\left( \sum_k \exp(o_k - \max(o_k)) \right)}.
	\end{aligned}
$$

## pytorch 中上述的过程

在 pytorch 中，softmax 与 cross entropy loss 是一起实现的，具体我们可以看 pytorch 的文档，其中对 nn.CrossEntropyLoss 是这样定义的：

$$
	\ell(x, y) = L = \{l_1,\dots,l_N\}^\top, \text{where}
$$

$$
	l_n = - w_{y_n} \log \frac{\exp(x_{n,y_n})}{\sum_{c=1}^C \exp(x_{n,c})} \cdot \mathbb{1}\{y_n \not= \text{ignore\_index}\}
$$

可以发现，其自带了 softmax 运算。

一般来说，我们会使用

```python
loss = nn.CrossEntropyLoss(reduction='none')
```

来计算。

至于这个 `reduction` 参数，其实就是保留 mini-batch 每个样本的值，返回一个向量。其他的选择：

$$
	\ell(x, y) = \begin{cases}
		\sum_{n=1}^N \frac{1}{\sum_{n=1}^N w_{y_n} \cdot \mathbb{1}\{y_n \not= \text{ignore\_index}\}} l_n, &
<<<<<<< HEAD
		\text{if reduction} = \text{`mean';}\\
		\sum_{n=1}^N l_n,  &
		\text{if reduction} = \text{`sum'.}
=======
		\text{if reduction} = \text{“mean";}\\
		\sum_{n=1}^N l_n,  &
		\text{if reduction} = \text{“sum".}
>>>>>>> prod
	\end{cases}
$$

## Reference

* https://zh-v2.d2l.ai/chapter_linear-networks/softmax-regression-concise.html
* https://pytorch.org/docs/master/generated/torch.nn.CrossEntropyLoss.html#torch.nn.CrossEntropyLoss
* https://github.com/pytorch/pytorch/blob/master/torch/csrc/api/include/torch/nn/modules/loss.h
* https://blog.csdn.net/goodxin_ie/article/details/89645358
