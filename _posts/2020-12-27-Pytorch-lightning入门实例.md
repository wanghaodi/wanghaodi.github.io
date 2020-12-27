---
layout:     post
title:      Pytorch-lightning入门实例
subtitle:   Pytorch-lightning
date:       2020-12-27
author:     HD
header-img: img/about-bg.jpg
catalog: true
tags:
    - Pytorch-lightning
    - Pytorch
---

# Pytorch-lightning入门实例

本文将通过Colab平台及MINIST数据集指导你了解Pytorch-lightning的核心组成。

**注意：任何的深度学习、机器学习的Pytorch工程都可以转变为lightning结构**

## 从MNIST到自动编码器

### 安装Lightning

虽然说安装Lightning非常的容易，但还是建议大家在本地通过conda来安装Lightning

```python
conda activate my_env
pip install pytorch-lightning
```

当你运行在在Google Colab上时，需要执行

```python
!pip install pytorch-lightning
```

![截屏2020-12-27 上午10.29.06](https://tva1.sinaimg.cn/large/0081Kckwly1gm27gmkwbmj323g0j6doq.jpg)

![截屏2020-12-27 上午10.29.19](https://tva1.sinaimg.cn/large/0081Kckwly1gm27gsoyglj31q0094mzy.jpg)

当出现上述内容是，代表Lightning已经安装完成。

或者你也可以使用conda命令来安装

```python
conda install pytorch-lightning -c conda-forge
```

### The research

#### The Model

Lightning由以下核心部分组成：

- The model
- The optimizers
- The train/val/test steps

我们通过Model引入这一部分，下面我们将会设计一个三层的神经网络模型

```python
import torch
from torch.nn import functional as F
from torch import nn
from pytorch_lightning.core.lightning import LightningModule

class LitMNIST(LightningModule):

  def __init__(self):
    super().__init__()

    # mnist images are (1, 28, 28) (channels, width, height)
    self.layer_1 = torch.nn.Linear(28 * 28, 128)
    self.layer_2 = torch.nn.Linear(128, 256)
    self.layer_3 = torch.nn.Linear(256, 10)

  def forward(self, x):
    batch_size, channels, width, height = x.size()

    # (b, 1, 28, 28) -> (b, 1*28*28)
    x = x.view(batch_size, -1)
    x = self.layer_1(x)
    x = F.relu(x)
    x = self.layer_2(x)
    x = F.relu(x)
    x = self.layer_3(x)

    x = F.log_softmax(x, dim=1)
    return x
```

可以观察到我们的LitMNIST类并不是像Pytorch中继承于`torch.nn.Module`类，而是Pytorch-lightning中的`LightningModule`类。该类与Pytorch中的`torch.nn.Module`类并没有太大的区别，只是增加了一些功能，我们可以像在Pytorch中使用`torch.nn.Module`类一样使用它。例如：

```python
net = LitMNIST()
x = torch.randn(1, 1, 28, 28)
out = net(x)
```

![截屏2020-12-27 上午10.39.35](https://tva1.sinaimg.cn/large/0081Kckwly1gm27rgrwejj31ao0agq4i.jpg)

下一步我们添加训练过程training_step，它继承于`LightningModule`，其中包含了所有训练过程中的逻辑内容

```python
class LitMNIST(LightningModule):

    def training_step(self, batch, batch_idx):
        x, y = batch
        logits = self(x)
        loss = F.nll_loss(logits, y)
        return loss
```

##### Data

LIghtning运行在dataloders上，以下是数据加载部分的代码：

```python
from torch.utils.data import DataLoader, random_split
from torchvision.datasets import MNIST
import os
from torchvision import datasets, transforms

# transforms
# prepare transforms standard to MNIST
transform=transforms.Compose([transforms.ToTensor(),
                              transforms.Normalize((0.1307,), (0.3081,))])

# data
mnist_train = MNIST(os.getcwd(), train=True, download=True, transform=transform)
mnist_train = DataLoader(mnist_train, batch_size=64)
```

我比较推荐采用下面的方式加载数据：

```python
class MNISTDataModule(pl.LightningDataModule):
    def __init__(self, batch_size=64):
        super().__init__()
        self.batch_size = batch_size

    def prepare_data(self):
        # download only
        MNIST(os.getcwd(), train=True, download=True, transform=transforms.ToTensor())
        MNIST(os.getcwd(), train=False, download=True, transform=transforms.ToTensor())

    def setup(self, stage):
        # transform
        transform=transforms.Compose([transforms.ToTensor()])
        mnist_train = MNIST(os.getcwd(), train=True, download=False, transform=transform)
        mnist_test = MNIST(os.getcwd(), train=False, download=False, transform=transform)

        # train/val split
        mnist_train, mnist_val = random_split(mnist_train, [55000, 5000])

        # assign to use in dataloaders
        self.train_dataset = mnist_train
        self.val_dataset = mnist_val
        self.test_dataset = mnist_test

    def train_dataloader(self):
        return DataLoader(self.train_dataset, batch_size=self.batch_size)

    def val_dataloader(self):
        return DataLoader(self.val_dataset, batch_size=self.batch_size)

    def test_dataloader(self):
        return DataLoader(self.test_dataset, batch_size=self.batch_size)
```

使用上面的DataModule可以更加方便的分项数据定义

```python
# use an MNIST dataset
mnist_dm = MNISTDatamodule()
model = LitModel(num_classes=mnist_dm.num_classes)
trainer.fit(model, mnist_dm)

# or other datasets with the same model
imagenet_dm = ImagenetDatamodule()
model = LitModel(num_classes=imagenet_dm.num_classes)
trainer.fit(model, imagenet_dm)
```

##### Optimizer

在Pytorch中，我们通过下列方式来Optimizer代码：

```python
from torch.optim import Adam
optimizer = Adam(LitMNIST().parameters(), lr=1e-3)
```

在Lightning中，方法类似，但是我们会将其包含在`configure_optimizers()`方法中

```python
class LitMNIST(LightningModule):

    def configure_optimizers(self):
        return Adam(self.parameters(), lr=1e-3)
```

##### Training step

我们将其写在training_step()`方法中

```python
class LitMNIST(LightningModule):

    def training_step(self, batch, batch_idx):
        x, y = batch
        logits = self(x)
        loss = F.nll_loss(logits, y)
        return loss
```

##### Training

目前为止，我们已经完成了四个主要的代码部分

```python
import torch
from torch.nn import functional as F
from torch import nn
from pytorch_lightning.core.lightning import LightningModule

class LitMNIST(LightningModule):

    def __init__(self):
        super().__init__()

        # mnist images are (1, 28, 28) (channels, width, height)
        self.layer_1 = torch.nn.Linear(28 * 28, 128)
        self.layer_2 = torch.nn.Linear(128, 256)
        self.layer_3 = torch.nn.Linear(256, 10)

    def forward(self, x):
        batch_size, channels, width, height = x.size()

        # (b, 1, 28, 28) -> (b, 1*28*28)
        x = x.view(batch_size, -1)
        x = self.layer_1(x)
        x = F.relu(x)
        x = self.layer_2(x)
        x = F.relu(x)
        x = self.layer_3(x)

        x = F.log_softmax(x, dim=1)
        return x

    def training_step(self, batch, batch_idx):
        x, y = batch
        logits = self(x)
        loss = F.nll_loss(logits, y)
        return loss

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=1e-3)

```

最后执行下列代码训练我们的数据：

```python
from pytorch_lightning import Trainer
dm = MNISTDataModule()
model = LitMNIST()
trainer = Trainer(gpus=8)
trainer.fit(model, dm)
```

训练时间比较长

## 小结

我在博客中的每篇文章都是我一字一句敲出来的，转载的文章我也注明了出处，表示对原作者的尊重。同时也希望大家都能尊重我的付出。

最后，也希望大家关注我的个人博客[HD Blog][1]

谢谢~

 



[1]: https://whdi.top