---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "PyTorch Learning Note-3"
subtitle: "[DEEP LEARNING WITH PYTORCH: A 60 MINUTE BLITZ] Neural Networks"
summary: "[DEEP LEARNING WITH PYTORCH: A 60 MINUTE BLITZ] Neural Networks"
authors: ["yuxinzhao"]
tags: ["pytorch", "deep learning"]
categories: ["deep-learning"]
date: 2019-07-22T20:48:56+08:00
lastmod: 2019-07-22T20:48:56+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: ["pytorch-learning"]
---

使用torch.nn包来构建神经网络。

`nn`包依赖`autograd`包来定义模型并求导。 一个`nn.Module`包含各个层和一个`forward(input)`方法，该方法返回`output`。

![img](image/68747470733a2f2f7079746f7263682e6f72672f7475746f7269616c732f5f696d616765732f6d6e6973742e706e67.png)

它是一个简单的前馈神经网络，它接受一个输入，然后一层接着一层地传递，最后输出计算的结果。

神经网络的典型训练过程如下：

1. 定义包含一些可学习的参数(或者叫权重)神经网络模型；
2. 在数据集上迭代；
3. 通过神经网络处理输入；
4. 计算损失(输出结果和正确值的差值大小)；
5. 将梯度反向传播回网络的参数；
6. 更新网络的参数，主要使用如下简单的更新原则： `weight = weight - learning_rate * gradient`

## 定义网络

```python
import torch
import torch.nn as nn
import torch.nn.functional as F 

class Net(nn.Module):

    def __init__(self):
        super(Net, self).__init__()

        # 1 input image channel
        # 6 output image channel
        # 5x5 square convolution kernel
        self.conv1 = nn.Conv2d(1, 6, 5)
        self.conv2 = nn.Conv2d(6, 16, 5)

        # an affine operation: y = Wx + b
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        # Max pooling over a (2, 2) window
        x = F.max_pool2d(F.relu(self.conv1(x)), (2, 2))
        # if the size is a square you can only specify a single number
        x = F.max_pool2d(F.relu(self.conv2(x)), 2)

        x = x.view(-1, self.num_flat_features(x))
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

    def num_flat_features(self, x):
        size = x.size()[1:]  # all dimensions except the batch dimension
        
        num_features = 1
        for s in size:
            num_features *= s
        return num_features
    
net = Net()
print(net)
```

```python
Net(
  (conv1): Conv2d(1, 6, kernel_size=(5, 5), stride=(1, 1))
  (conv2): Conv2d(6, 16, kernel_size=(5, 5), stride=(1, 1))
  (fc1): Linear(in_features=400, out_features=120, bias=True)
  (fc2): Linear(in_features=120, out_features=84, bias=True)
  (fc3): Linear(in_features=84, out_features=10, bias=True)
)
```

**在模型中必须要定义 `forward` 函数**，`backward` 函数（用来计算梯度）会被`autograd`自动创建。 可以在 `forward` 函数中使用任何针对 Tensor 的操作。

**`net.parameters()`返回可被学习的参数（权重）列表和值**

```python
params = list(net.parameters())
print(len(params))
print(params[0].size())  # params[0] == net.conv1.weight
```

```python
10
torch.Size([6, 1, 5, 5]
```

**`net.named_parameters`可同时返回可学习的参数及名称。**

```python
for name,parameters in net.named_parameters():
    print(name,':',parameters.size())
```

```python
conv1.weight : torch.Size([6, 1, 5, 5])
conv1.bias : torch.Size([6])
conv2.weight : torch.Size([16, 6, 5, 5])
conv2.bias : torch.Size([16])
fc1.weight : torch.Size([120, 400])
fc1.bias : torch.Size([120])
fc2.weight : torch.Size([10, 120])
fc2.bias : torch.Size([10])
```



测试随机输入32×32。 注：这个网络（LeNet）期望的输入大小是32×32，如果使用MNIST数据集来训练这个网络，请把图片大小重新调整到32×32。

```python
input = torch.randn(1, 1, 32, 32)
out = net(input)
print(out)
```

```python
tensor([[ 0.1052, -0.0361,  0.1122,  0.1072,  0.0887,  0.0477,
 0.0916, -0.0594,
         -0.1450,  0.0574]], grad_fn=<AddmmBackward>)
```

将所有参数的梯度缓存清零，然后进行随机梯度的的反向传播：

```python
net.zero_grad()
out.backward(torch.randn(1, 10))
```

{{% alert note %}}

``torch.nn`` 只支持小批量输入。整个 ``torch.nn`` 包都只支持小批量样本，而不支持单个样本。 例如，``nn.Conv2d`` 接受一个4维的张量， ``每一维分别是sSamples * nChannels * Height * Width（样本数*通道数*高*宽）``。 **如果你有单个样本，只需使用 ``input.unsqueeze(0)`` 来添加其它的维数**

{{% /alert %}}

我们回顾一下到目前为止用到的类。

**回顾:**

- `torch.Tensor`：一个用过自动调用 `backward()`实现支持自动梯度计算的 *多维数组* ， 并且保存关于这个向量的梯度
- `nn.Module`：神经网络模块。封装参数、移动到GPU上运行、导出、加载等。
- `nn.Parameter`：一种变量，当把它赋值给一个`Module`时，被 自动地注册为一个参数。
- `autograd.Function`：实现一个自动求导操作的前向和反向定义，每个变量操作至少创建一个函数节点，每一个`Tensor`的操作都h会创建一个接到创建`Tensor`和 编码其历史 的函数的`Function`节点。

## 损失函数

一个损失函数接受一对 (output, target) 作为输入，计算一个值来估计网络的输出和目标值相差多少。

[nn包](https://pytorch.org/docs/nn)中有很多不同的[损失函数](https://pytorch.org/docs/nn.html#loss-functions)。 `nn.MSELoss`是一个比较简单的损失函数，它计算输出和目标间的**均方误差**， 例如：

```python
input = torch.randn(1, 1, 32, 32)
output = net(input)
target = torch.randn(10)  # 随机值作为样例
target = target.view(1, -1)  # 使target和output的shape相同

criterion = nn.MSELoss()
loss = criterion(output, target)
print(loss)

```

```python
tensor(1.1509, grad_fn=<MseLossBackward>)
```

### 常见损失函数

#### `nn.L1Loss`

$$
loss(x, y) = 
\begin{cases}
\frac1n \sum \left| x_i - y_i \right|, & \text{if reduction='mean'} \\\\\\
\sum \left| x_i - y_i \right|, & \text{if reduction='sum'}
\end{cases}
$$

#### `nn.NLLLoss`

**Negative Log Liklihood(NLL) Loss f 负对数似然损失函数**
$$
loss(x, class) = -x_{class}
$$
#### `nn.MSELoss`

**Mean Squrare Error(MSE) Loss 均方损失函数**
$$
loss(x, y) = \frac1n \sum(x_i - y_i)^2
$$

#### `nn.CrossEntropyLoss`

多分类用的交叉熵损失函数，LogSoftMax和NLLLoss集成到一个类中，会调用nn.NLLLoss函数,可以理解为CrossEntropyLoss()=log_softmax() + NLLLoss()


$$
loss(x, class) = -\log \frac{\\exp\(x\_{class}\)}{\\sum\_j \\exp(x\_j)} = - x_{class} + \log \left(\sum_j \exp(x_j) \right)
$$

#### `nn.BCELoss`

Binary Cross Entropy
$$
loss(x, t) = -\frac1n \sum_i \left(t_i \*\log(x_i) + (1-t_i)\*\log(1-x_i) \right) 
$$




## 反向传播

调用`loss.backward()`获得反向传播的误差。

但是在调用前需要清除已存在的梯度，否则梯度将被累加到已存在的梯度。

现在，我们将调用`loss.backward()`，并查看conv1层的偏差（bias）项在反向传播前后的梯度。

```python
net.zero_grad()  # 清除梯度

print('conv1.bias.grad before backward')
print(net.conv1.bias.grad)

loss.backward()

print('conv1.bias.grad after backward')
print(net.conv1.bias.grad)
```

```python
conv1.bias.grad before backward
None
conv1.bias.grad after backward
tensor([ 0.0027, -0.0142,  0.0197,  0.0021, -0.0018,  0.0001])
```

## 更新权重

在实践中最简单的权重更新规则是随机梯度下降（SGD）：

```python
 weight = weight - learning_rate * gradient
```

我们可以使用简单的Python代码实现这个规则：

```python
learning_rate = 0.01
for f in net.parameters():
    f.data.sub_(f.grad.data * learning_rate)
```

但是当使用神经网络是想要使用各种不同的更新规则时，比如SGD、Nesterov-SGD、Adam、RMSPROP等，PyTorch中构建了一个包`torch.optim`实现了所有的这些规则。 使用它们非常简单：

```python
import torch.optim as optim

# create your optimizer
optimizer = optim.SGD(net.parameters(), lr=0.01)
criterion = nn.MSELoss()

# in your training loop
optimizer.zero_grad()   # zero the gradient buffers
output = net(input)
loss = criterion(output, target)
loss.backward()    # calculate gradients
optimizer.step()   # Does the update
```

