---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "PyTorch Learning Note-4"
subtitle: "[DEEP LEARNING WITH PYTORCH: A 60 MINUTE BLITZ] Training A Classifier"
summary: "[DEEP LEARNING WITH PYTORCH: A 60 MINUTE BLITZ] Training A Classifier"
authors: ["yuxinzhao"]
tags: ["pytorch", "deep learning"]
categories: ["deep-learning"]
date: 2019-07-23T09:40:02+08:00
lastmod: 2019-07-23T09:40:02+08:00
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



一般情况下处理图像、文本、音频和视频数据时，可以使用标准的Python包来加载数据到一个numpy数组中。 然后把这个数组转换成 `torch.*Tensor`。

- 图像可以使用 Pillow, OpenCV
- 音频可以使用 scipy, librosa
- 文本可以使用原始Python和Cython来加载，或者使用 NLTK或 SpaCy 处理

特别的，对于图像任务，`torchvision` 包 包含了处理一些基本图像数据集的方法。这些数据集包括 Imagenet, CIFAR10, MNIST 等。除了数据加载以外，`torchvision` 还包含了图像转换器， `torchvision.datasets` 和 `torch.utils.data.DataLoader`。

`torchvision`包不仅提供了巨大的便利，也避免了代码的重复。

以下使用的案例中，将使用CIFAR10数据集，它有如下10个类别 ：‘airplane’, ‘automobile’, ‘bird’, ‘cat’, ‘deer’, ‘dog’, ‘frog’, ‘horse’, ‘ship’, ‘truck’。CIFAR-10的图像都是 3x32x32大小的，即，3颜色通道，32x32像素。

![img](https://camo.githubusercontent.com/4ed11e8caab93da7138bafcf8d14441abe097151/68747470733a2f2f7079746f7263682e6f72672f7475746f7269616c732f5f696d616765732f636966617231302e706e67)

# 训练一个图像分类器

依次按照下列顺序进行：

1. 使用`torchvision`加载和归一化CIFAR10训练集和测试集
2. 定义一个卷积神经网络
3. 定义损失函数
4. 在训练集上训练网络
5. 在测试集上测试网络

## 读取和归一化 CIFAR10

使用`torchvision`可以非常容易地加载CIFAR10。

```python
import torch
import torchvision
import torchvision.transforms as transforms 
```

torchvision的输出是[0,1]的PILImage图像，我们把它转换为归一化范围为[-1, 1]的张量。

```python
transform = transforms.Compose(
    [transforms.ToTensor(),
     transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])

trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=True, transform=transform)
trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

testset = torchvision.datasets.CIFAR10(root='./data', train=False, download=True, transform=transform)
testloader = torch.utils.data.DataLoader(testset, batch_size=4, shuffle=False, num_workers=2)

classes = ('plane', 'car', 'bird', 'cat', 'deer', 'dog', 'frog', 'horse', 'ship', 'truck')
```

## 展示一些训练图像

```python
import matplotlib.pyplot as plt 
import numpy as np 

# 展示图像的函数
def imshow(img):
    img = img / 2 + 0.5  # unnormalize
    npimg = img.numpy()
    plt.imshow(np.transpose(npimg, (1, 2, 0)))
    plt.show()

# 获取随机数据
dataiter = iter(trainloader)
images, labels = dataiter.next()

# 展示图像
imshow(torchvision.utils.make_grid(images))

# 显示图像标签
print(" ".join("%5s" % classes[labels[j]] for j in range(4)))
```

![1563855333962](image/1563855333962.png)

```python
truck horse   dog  frog
```

## 定义一个卷积神经网络

```python
import torch.nn as nn
import torch.nn.functional as F 

class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        
        self.conv1 = nn.Conv2d(3, 6, 5) # 3 input channels, 6 output channels, 5x5 convolutional kernel
        self.pool = nn.MaxPool2d(2, 2)  # 池化层可以共用
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
    
net = Net()
```

## 定义损失函数和优化器

```python
import torch.optim as optim

criterion = nn.CrossEntropyLoss()
optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
```

## 训练网络

我们只需在数据迭代器上循环，将数据输入给网络，并优化。

```python
for epoch in range(2):  # 多批次循环

    running_loss = 0.0
    for i, data in enumerate(trainloader, 0):
        # 获取输入
        inputs, labels = data

        # 梯度置0
        optimizer.zero_grad()

        # 正向传播，反向传播，优化
        outputs = net(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        # 打印状态信息
        running_loss += loss.item()
        if i % 2000 == 1999: # 每2000批次打印一次
            print('[%d, %5d] loss: %.3f' % (epoch + 1, i + 1, running_loss / 2000))
            running_loss = 0.0

print("Finished Training")
```

## 在测试集上测试网络

我们在整个训练集上进行了2次训练，但是我们需要检查网络是否从数据集中学习到有用的东西。 通过预测神经网络输出的类别标签与实际情况标签进行对比来进行检测。 如果预测正确，我们把该样本添加到正确预测列表。 第一步，显示测试集中的图片并熟悉图片内容。然后用

```python
dataiter = iter(testloader)
images, labels = dataiter.next()

# 输出是10个标签的能量。 一个类别的能量越大，神经网络越认为它是这个类别。所以让我们得到最高能量的标签。
outputs = net(images) 
_, predicted = torch.max(outputs, 1) # (maxvalues, indices) is returned

# 显示图片
imshow(torchvision.utils.make_grid(images))
print('GroundTruth: ', ' '.join('%5s' % classes[labels[j]] for j in range(4)))
print('Predicted:   ', ' '.join('%5s' % classes[predicted[j]] for j in range(4)))
```

![1563857966974](image/1563857966974.png)

```python
GroundTruth:    cat  ship  ship plane
Predicted:     bird plane plane   dog
```

接下来让看看网络在整个测试集上的结果如何。

```python
correct = 0
total = 0
with torch.no_grad():
    for data in testloader:
        images, labels = data
        outputs = net(images)
        _, predicted = torch.max(outputs.data, 1)
        total += labels.size(0)
        correct += (predicted == labels).sum().item()

print('Accuracy of the network on the 10000 test images: %d %%' % (100 * correct / total))
```

```python
Accuracy of the network on the 10000 test images: 49 %
```



在识别哪一类的时候好，哪一类不好呢？

```python
class_correct = list(0. for i in range(10))
class_total = list(0. for i in range(10))
with torch.no_grad():
    for data in testloader:
        images, labels = data
        outputs = net(images)
        _, predicted = torch.max(outputs, 1)
        c = (predicted == labels).squeeze()
        for i in range(4):
            label = labels[i]
            class_correct[label] += c[i].item()
            class_total[label] += 1

for i in range(10):
    print('Accuracy of %5s : %2d %%' % (classes[i], 100 * class_correct[i] / class_total[i]))
```

```python
Accuracy of plane : 69 %
Accuracy of   car : 69 %
Accuracy of  bird : 15 %
Accuracy of   cat : 43 %
Accuracy of  deer : 44 %
Accuracy of   dog : 22 %
Accuracy of  frog : 72 %
Accuracy of horse : 58 %
Accuracy of  ship : 41 %
Accuracy of truck : 58 %
```



## 在GPU上训练

把一个神经网络移动到GPU上训练就像把一个Tensor转换GPU上一样简单。并且这个操作会递归遍历有所模块，并将其参数和缓冲区转换为CUDA张量。

```python
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")

# 确认我们的电脑支持CUDA，然后显示CUDA信息
print(device)

# 将递归遍历所有模块并将模块的参数和缓冲区 转换成CUDA张量
net.to(device)
```

记住: `inputs` 和 `labels` 也要转换。

```python
inputs, labels = inputs.to(device), labels.to(device)
```

