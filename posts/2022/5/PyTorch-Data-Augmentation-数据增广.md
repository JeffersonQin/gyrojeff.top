---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-05-02 23:15:26'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-05-02 23:15:26'
parent: 0
password: ''
slug: PyTorch-Data-Augmentation-数据增广
status: publish
tags: [DL, ML, 机器学习, 深度学习]
template: ''
title: PyTorch Data Augmentation 数据增广
type: post
---
## Data Augmentation

这个词其实很容易误导人，因为 Augmentation 是增广的意思。数据增广，自然而然就会让人理解为：“增加了新的数据”。然而在实现的过程中，不一定是这样。

我们会给定一些随机化的参数，在每个 epoch 内生成随机增广后的图片。

比方说，每个 epoch 都按照一定的 random rule 来 crop 图片、调整色调，便是图像增广。

## PyTorch 实现

这里拿 Fashion-MNIST 举例，同样我们会给出例子来证明我们的做法是行之有效的。

首先引入包：

```python
%matplotlib inline
%config InlineBackend.figure_formats = ['svg']
from matplotlib import pyplot as plt
import torch
import torchvision
from torch.utils import data
```

加载 FashionMNIST 数据集，并且禁用 shuffle，这样保证我们每个 epoch 图片顺序都是相同的，方便我们验证：

```python
def load_data_fashion_mnist(batch_size, dataloader_worker_count, resize=None):
    trans = [torchvision.transforms.ToTensor()]
    if resize:
        trans.insert(0, torchvision.transforms.Resize(resize))
    trans.append(torchvision.transforms.RandomCrop((28, 28)))
    trans = torchvision.transforms.Compose(trans)
    mnist_train = torchvision.datasets.FashionMNIST(
        root="../data", train=True, transform=trans, download=True)
    mnist_test = torchvision.datasets.FashionMNIST(
        root="../data", train=False, transform=trans, download=True)
    return (data.DataLoader(mnist_train, batch_size, shuffle=False, num_workers=dataloader_worker_count),
            data.DataLoader(mnist_test, batch_size, shuffle=False, num_workers=dataloader_worker_count))
```

辅助渲染的函数：

```python
def get_fashion_mnist_labels(labels):
    '''
    返回Fashion-MNIST数据集的文本标签
    '''
    text_labels = ['t-shirt', 'trouser', 'pullover', 'dress', 'coat', 'sandal', 'shirt', 'sneaker', 'bag', 'ankle boot']
    return [text_labels[int(i)] for i in labels]

def show_images(imgs, num_rows, num_cols, titles=None, scale=1.2):
    '''
    绘制图像列表
    '''
    figsize = (num_cols * scale, num_rows * scale)
    _, axes = plt.subplots(num_rows, num_cols, figsize=figsize)
    axes = axes.flatten()
    for i, (ax, img) in enumerate(zip(axes, imgs)):
        if torch.is_tensor(img):
            # 图片张量
            ax.imshow(img.numpy())
        else:
            # PIL图片
            ax.imshow(img)
        ax.axes.get_xaxis().set_visible(False)
        ax.axes.get_yaxis().set_visible(False)
        if titles:
            ax.set_title(titles[i])
    return axes
```

测试三个 epoch，看每次 epoch 第一个 batch 的数据：

```python
train_iter, test_iter = load_data_fashion_mnist(18, 4, resize=40)

for _ in range(3):
	for i, (X, y) in enumerate(train_iter):
		if i == 0:
			show_images(X.reshape(18, 28, 28), 2, 9, titles=get_fashion_mnist_labels(y))
```

输出：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20220416162716.png)

可以看出有明显的不同。

本文中的 notebook：https://github.com/JeffersonQin/deep-learning/blob/master/tests/pytorch-data-augmentation.ipynb
