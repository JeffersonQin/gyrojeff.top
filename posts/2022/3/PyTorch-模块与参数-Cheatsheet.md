---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2022-03-30 09:49:10'
fields: {customSummary: '', mathjax: auto, noThumbInfoStyle: default, outdatedNotice: 'no',
  reprint: standard, thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2022-03-30 09:49:10'
parent: 0
password: ''
slug: PyTorch-模块与参数-Cheatsheet
status: publish
tags: [DL, ML, PyTorch, 机器学习, 深度学习]
template: ''
title: PyTorch 模块与参数 Cheatsheet
type: post
---
- [Preface](#preface)
- [Define Network](#define-network)
- [Custom Module](#custom-module)
- [Custom Module with Params](#custom-module-with-params)
- [Implement Sequential](#implement-sequential)
- [Custom Forward Function](#custom-forward-function)
- [Access Parameter](#access-parameter)
- [Access All Params](#access-all-params)
- [Nested Network Params](#nested-network-params)
- [Parameter Initialization](#parameter-initialization)
- [Share Parameters](#share-parameters)
- [Save & Load Network Params](#save--load-network-params)
- [GPU Info](#gpu-info)
- [GPU Device](#gpu-device)
- [Network on GPU](#network-on-gpu)

## Preface

这块内容繁多冗长，每次查教程也很繁琐，所以就直接整理成 Cheatsheet了。

代码全部出自 d2l chapter 5.

## Define Network

```python
import torch
from torch import nn
from torch.nn import functional as F

net = nn.Sequential(nn.Linear(20, 256), nn.ReLU(), nn.Linear(256, 10))

X = torch.rand(2, 20)
net(X)
```

```
tensor([[-0.1216,  0.0153,  0.0546, -0.0989, -0.0582,  0.1448, -0.3097, -0.0478,
         -0.1381,  0.0593],
        [-0.1315,  0.0540,  0.0157, -0.0701, -0.2307,  0.0710, -0.2731, -0.0527,
         -0.2170,  0.1010]], grad_fn=<AddmmBackward0>)
```

## Custom Module

```python
class MLP(nn.Module):
    # 用模型参数声明层。这里，我们声明两个全连接的层
    def __init__(self):
        # 调用MLP的父类Module的构造函数来执行必要的初始化。
        # 这样，在类实例化时也可以指定其他函数参数，例如模型参数params（稍后将介绍）
        super().__init__()
        self.hidden = nn.Linear(20, 256)  # 隐藏层
        self.out = nn.Linear(256, 10)  # 输出层

    # 定义模型的前向传播，即如何根据输入X返回所需的模型输出
    def forward(self, X):
        # 注意，这里我们使用ReLU的函数版本，其在nn.functional模块中定义。
        return self.out(F.relu(self.hidden(X)))

net = MLP()
net(X)
```

```
tensor([[ 0.0097,  0.0207,  0.1453, -0.0685,  0.0505,  0.2176,  0.0180, -0.2566,
          0.1506, -0.0075],
        [ 0.0425, -0.0261,  0.1969,  0.0842,  0.0037,  0.1542, -0.0176, -0.1798,
          0.0179, -0.1200]], grad_fn=<AddmmBackward0>)
```

## Custom Module with Params

PyTorch 会自动识别参数 (`nn.Parameter`)

```python
class MyLinear(nn.Module):
    def __init__(self, in_units, units):
        super().__init__()
        self.weight = nn.Parameter(torch.randn(in_units, units))
        self.bias = nn.Parameter(torch.randn(units,))
    def forward(self, X):
        linear = torch.matmul(X, self.weight.data) + self.bias.data
        return F.relu(linear)

linear = MyLinear(5, 3)
linear.weight
```

```
Parameter containing:
tensor([[-2.2981, -1.8825, -0.9347],
        [ 0.1222, -1.0374,  1.1512],
        [-0.2859, -0.0680,  0.9072],
        [ 1.2177, -0.8947,  0.6278],
        [ 1.4800,  0.5804, -0.9661]], requires_grad=True)
```



## Implement Sequential

```python
class MySequential(nn.Module):
    def __init__(self, *args):
        super().__init__()
        for idx, module in enumerate(args):
            # 这里，module是Module子类的一个实例。我们把它保存在'Module'类的成员
            # 变量_modules中。module的类型是OrderedDict
            self._modules[str(idx)] = module

    def forward(self, X):
        # OrderedDict保证了按照成员添加的顺序遍历它们
        for block in self._modules.values():
            X = block(X)
        return X

net = MySequential(nn.Linear(20, 256), nn.ReLU(), nn.Linear(256, 10))
net(X)
```

```
tensor([[ 0.0978,  0.0979,  0.1417, -0.3019,  0.0232, -0.0246,  0.0881, -0.0908,
         -0.0428, -0.0388],
        [ 0.1624,  0.0251, -0.0085, -0.3342,  0.0645, -0.1956,  0.1111, -0.0802,
         -0.1252,  0.0666]], grad_fn=<AddmmBackward0>)
```

## Custom Forward Function

```python
class FixedHiddenMLP(nn.Module):
    def __init__(self):
        super().__init__()
        # 不计算梯度的随机权重参数。因此其在训练期间保持不变
        self.rand_weight = torch.rand((20, 20), requires_grad=False)
        self.linear = nn.Linear(20, 20)

    def forward(self, X):
        X = self.linear(X)
        # 使用创建的常量参数以及relu和mm函数
        X = F.relu(torch.mm(X, self.rand_weight) + 1)
        # 复用全连接层。这相当于两个全连接层共享参数
        X = self.linear(X)
        # 控制流
        while X.abs().sum() > 1:
            X /= 2
        return X.sum()

net = FixedHiddenMLP()
net(X)
```

```
tensor(-0.0431, grad_fn=<SumBackward0>)
```

注：关于参数复用，专门有文章讨论。

## Access Parameter

对于这个网络：

```python
import torch
from torch import nn

net = nn.Sequential(nn.Linear(4, 8), nn.ReLU(), nn.Linear(8, 1))
X = torch.rand(size=(2, 4))
net(X)
```

```
tensor([[0.2621],
        [0.3079]], grad_fn=<AddmmBackward0>)
```

第三层的全部参数：

```python
net[2].state_dict()
```

```
OrderedDict(
	[
		('weight', tensor([[ 0.0743,  0.1876,  0.0571,  0.3447,  0.3483, -0.2867,  0.3273, -0.1527]])), 
		('bias', tensor([0.1162]))
	]
)
```

指定访问：

```python
print(type(net[2].bias))
print(net[2].bias)
print(net[2].bias.data)
```

```
<class 'torch.nn.parameter.Parameter'>
Parameter containing:
tensor([0.1162], requires_grad=True)
tensor([0.1162])
```

## Access All Params

```python
net[0].named_parameters()
net.named_parameters()
net.state_dict()
```

## Nested Network Params

如果网络有嵌套，或者有自定义参数 (`nn.Parameter`)，PyTorch 会自动识别所有参数。

```python
def block1():
    return nn.Sequential(nn.Linear(4, 8), nn.ReLU(),
                         nn.Linear(8, 4), nn.ReLU())

def block2():
    net = nn.Sequential()
    for i in range(4):
        # 在这里嵌套
        net.add_module(f'block {i}', block1())
    return net

rgnet = nn.Sequential(block2(), nn.Linear(4, 1))

print(rgnet)
```

```
Sequential(
  (0): Sequential(
    (block 0): Sequential(
      (0): Linear(in_features=4, out_features=8, bias=True)
      (1): ReLU()
      (2): Linear(in_features=8, out_features=4, bias=True)
      (3): ReLU()
    )
    (block 1): Sequential(
      (0): Linear(in_features=4, out_features=8, bias=True)
      (1): ReLU()
      (2): Linear(in_features=8, out_features=4, bias=True)
      (3): ReLU()
    )
    (block 2): Sequential(
      (0): Linear(in_features=4, out_features=8, bias=True)
      (1): ReLU()
      (2): Linear(in_features=8, out_features=4, bias=True)
      (3): ReLU()
    )
    (block 3): Sequential(
      (0): Linear(in_features=4, out_features=8, bias=True)
      (1): ReLU()
      (2): Linear(in_features=8, out_features=4, bias=True)
      (3): ReLU()
    )
  )
  (1): Linear(in_features=4, out_features=1, bias=True)
)
```

```python
rgnet[0][1][0].bias.data
```

```
tensor([ 0.3572,  0.2251, -0.3531, -0.0630,  0.4908, -0.4802, -0.3679, -0.2210])
```

## Parameter Initialization

Normal Distribution:

```python
def init_normal(m):
    if type(m) == nn.Linear:
        nn.init.normal_(m.weight, mean=0, std=0.01)
        nn.init.zeros_(m.bias)
net.apply(init_normal)
net[0].weight.data[0], net[0].bias.data[0]
```

Constant:

```python
def init_constant(m):
    if type(m) == nn.Linear:
        nn.init.constant_(m.weight, 1)
        nn.init.zeros_(m.bias)
net.apply(init_constant)
net[0].weight.data[0], net[0].bias.data[0]
```

Xavier and constant combination:

```python
def xavier(m):
    if type(m) == nn.Linear:
        nn.init.xavier_uniform_(m.weight)
def init_42(m):
    if type(m) == nn.Linear:
        nn.init.constant_(m.weight, 42)

net[0].apply(xavier)
net[2].apply(init_42)
print(net[0].weight.data[0])
print(net[2].weight.data)
```

Custom Initialization:

$$
	\begin{aligned}
		w \sim \begin{cases}
			U(5, 10) & \text{ 可能性 } \frac{1}{4} \\
				0    & \text{ 可能性 } \frac{1}{2} \\
			U(-10, -5) & \text{ 可能性 } \frac{1}{4}
		\end{cases}
	\end{aligned}
$$

```python
def my_init(m):
    if type(m) == nn.Linear:
        print("Init", *[(name, param.shape)
                        for name, param in m.named_parameters()][0])
        nn.init.uniform_(m.weight, -10, 10)
        m.weight.data *= m.weight.data.abs() >= 5

net.apply(my_init)
```

## Share Parameters

```python
# 我们需要给共享层一个名称，以便可以引用它的参数
shared = nn.Linear(8, 8)
net = nn.Sequential(nn.Linear(4, 8), nn.ReLU(),
                    shared, nn.ReLU(),
                    shared, nn.ReLU(),
                    nn.Linear(8, 1))
```

## Save & Load Network Params

```python
torch.save(net.state_dict(), 'mlp.params')

clone = MLP()
clone.load_state_dict(torch.load('mlp.params'))
clone.eval()
```

## GPU Info

```python
!nvidia-smi
```

## GPU Device

这里定义了两个函数：

```python
def try_gpu(i=0):  #@save
    """如果存在，则返回gpu(i)，否则返回cpu()"""
    if torch.cuda.device_count() >= i + 1:
        return torch.device(f'cuda:{i}')
    return torch.device('cpu')

def try_all_gpus():  #@save
    """返回所有可用的GPU，如果没有GPU，则返回[cpu(),]"""
    devices = [torch.device(f'cuda:{i}')
             for i in range(torch.cuda.device_count())]
    return devices if devices else [torch.device('cpu')]

try_gpu(), try_gpu(10), try_all_gpus()
```

```
(device(type='cuda', index=0),
 device(type='cpu'),
 [device(type='cuda', index=0), device(type='cuda', index=1)])
```

## Network on GPU

```python
net = nn.Sequential(nn.Linear(3, 1))
net = net.to(device=try_gpu())
```
