---
layout:     post
title:      CS224W-图神经网络 笔记5.3：Spectral Clustering - 谱图聚类的具体操作步骤
subtitle:   Graph Machine Learning 
date:       2021-03-16
author:     HD
header-img: img/graph11.png
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记5.3：Spectral Clustering - 谱图聚类的具体操作步骤

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整** 
>
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/) 
>
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=5)

[toc]

## 引言

通过前一篇文章介绍，我们知道如何评价图划分的好坏；和为什么能根据图拉普拉斯矩阵的第二小特征值对应的特征向量对图进行近似分割。前面算是理论部分，下面进入实践部分。

## 1 谱聚类流程

### 1.1 谱聚类步骤

谱聚类过程分为三个步骤。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go72apn1l1j20ou0eztar.jpg)

第三步划分组，对倒数第二小特征向量进行升序排列后，如何确定分割的点呢？

- 基础操作

- - 取 `0点` 作为分割点，（正负号）
  - 取 `中位数`作为分割点

- 其他 （相对复杂方法）

- - 逐步计算，选择使得Normalied cut 最小的分割点。（Sweep procedure）

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go72aq0knpj20mc0b0q3q.jpg)

### 1.2 如何实现 K 分类呢？

上面介绍的都是二分的情况，如何推广到多（K）分的情况呢？

具体，有2个基本的方法：

1. 以分层划分的方式递归地调用二分类算法

2. - 缺点是效率低，不稳定（每一次都是近似，误差会放大）。

3. 采用多个特征向量的谱聚类

- 按照$\lambda$的值（排除最小的 λ）从小到大依次取 m 个特征向量，将节点嵌入到低维空间中，每个节点通过 m 维数据表示，之后通过 K-means 算法进行聚类。通常也不需要取很大的值。**多个特征向量，减少信息丢失，并已被证明能够近似地逼近最优划分。**

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go72ctziuyj20kw0d2js4.jpg)

### 1.3 如何选择社区的数量 K？

#### 1.3.1 特征间距

可将特征值从大到小排序后，两个连续的特征值之间的差被称为`特征间距（Eigengap）`。

通常来说，通过选取`最大化特征差距`的 K 大概率能获得稳定的划分结果。如下图对应的K=2是特征间距最大。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go72j2s4y8j20pa0b8js6.jpg)

## 2 基于motif 的图划分

第三节中有介绍到motif，将图拆解为一个个子图来重新看待网络，motif给了网络一个新的定义方式，可以考虑从motif的角度（而不是上述边的角度）出发来进行谱图聚类。

具体操作过程与基于边分割类似，不同的是需要对于划分标准，拉普拉斯矩阵进行转换。

### 2.1 Motif Conductance

对于最优划分标准类比edge cut和conductance，针对motif思想也是同样，就是要组内motif尽可能多，组间motif尽可能少。具体对比定义如下

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go72ke47iwj20nb09o404.jpg)

所以问题就变成了，给定motif M和图G，如何划分节点，使motif conductance最小。但找到最小motif conductance也为np问题，也需要采用算法近似估算

### 2.2 Motif Spectral Clustering

类比一般谱图聚类，基于motif的谱聚类也分为三步：

**1. 数据预处理**：基于motif对图边权重进行重新定义，**每个边权重为出现过的motif次数**.得到权重矩阵和拉普拉斯矩阵。

**2. 分解**：类似于标准的谱图聚类方法，计算拉普拉斯矩阵和对应的特征值特征向量

**3. 分组**：利用Sweep procedure方法，对第二小的特征值对应的特征向量x的元素从小到达排列，计算每一种划分下的motif conductance，选择使motif conductance最小的划分。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go72ll5vxoj20rj0fvq6d.jpg)如下图左下角所示，当r=5时，motif conductance最小。

此外，最小 motif conductance 也可以根据cheeger 不等式。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go72llcvm9j20h307bjrq.jpg)

基于motif 的谱聚类算法的价值：

- 它提供了从更高维度去观察网络社区结构的新角度。
- 算法简单、快速和灵活，便于应用。

### 2.3 基于motif的谱聚类案例

课上老师给了一个食物链网络中基于motif的谱图聚类结果。可以看出，基于motif的聚类在每一类结果中捕捉了特定的motif的结构，在每一类内部有较多的给定motif，而类与类之间这种motif较少。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go72mggchmj20sv0fxn4j.jpg)

## 参考文章

1. https://blog.csdn.net/klcola/article/details/104800804
2. 图网络机器学习 | 社区发现 — 谱聚类算法
3. 斯坦福CS224W 图与机器学习5】Spectral Clustering
4. 谱聚类方法推导和对拉普拉斯矩阵的理解
5. https://linalg.apachecn.org/#/docs/chapter21
6. https://www.cnblogs.com/xingshansi/p/6702188.html?utm_source=itdadao&utm_medium=referral
7. http://www.cs.yale.edu/homes/spielman/sgta/SpectTut.pdf
8. http://www.math.ucsd.edu/~fan/wp/cheeger.pdf