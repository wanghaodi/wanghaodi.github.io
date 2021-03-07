---
layout:     post
title:      CS224W-图神经网络 笔记5.2：Spectral Clustering - 谱聚类主要思想及关键结论的证明
subtitle:   Graph Machine Learning 
date:       2021-03-10
author:     HD
header-img: img/graph10.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记5.2：Spectral Clustering - 谱聚类主要思想及关键结论的证明

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整** 
>
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/) 
>
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=5)

[toc]

## 引言

本节除了介绍图划分（二分）的基本思路外，主要回答一个问题：**为什么能根据图拉普拉斯矩阵的第二小特征值对应的特征向量对图进行分割？**

## 1 图划分

图划分也要回答两个问题：

1. 怎么定义图的一个好的划分？（`标准`）
2. 如何高效得到划分结果？（`效率`）

### 1.1 图划分标准

先以最简单的图对分（bi-partition）为例，（对于多分情况可以在二分的基础上推广）。评价指标有：

#### 1. 割集规模（Graph Cuts）

Cut是将被分割的边的数量**最少**为图分割的标准，这一标准存在一定问题

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go5a7tobf0j20sa0bhgo5.jpg)

#### 2. 传导性（Conductance）

传导性不光追求被分割边数量最少，还兼顾组内的连接。衡量了组间连通性相对于每个组的组内连通性的程度。是个更好的图分割标准。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go5a7uldn8j20te0bejub.jpg)

虽然依据conductance可以获得较为平衡的图划分，但是**计算conductance 是NP-hard问题**。

除了这两个划分标准外，还有如在图像分割领域中用的比较多的Normalized cut（N-cut）等。

## 2 如何近似求解

要理解谱聚类算法需要掌握三个关键结论的证明。

### 2.1 一个不等式

这是一个关于对称矩阵瑞利商性质的证明。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go5ad9koevj20fz05smxf.jpg)

`瑞利定理（Rayleigh theorem）`相关介绍课参考下面引用文章1

### 2.2 近似优化方法

回到寻找最优划分解的问题，1973年 Fiedler 提出将二划分的集合 A 和 B 的元素标签限制在 1 和 -1，且限制 2 个集合的元素个数相同（等价于与向量（1, 1, …, 1）垂直）可以实现最优图分割的目的。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go5aewuio9j20jw0cnwfk.jpg)

**由于无法在求解的过程中严格满足上述条件(约束条件过分严格)**，故对向量取值弱化约束的`松弛法(relaxation)`，允许它们取任意实数。根据上面证明的瑞利定理（Rayleigh Theorem）提出最小化 Fiedler 提及的公式，就是求解拉普拉斯矩阵L的第二小的特征值 $\lambda_2$ 所对应的特征向量x .x是最优分割向量的近似。可以通过x各维度的值的正负符号来决定相应节点所属的社区。

ps：为什么不是最小特征值对应的特征向量呢？

因为，图拉普拉斯矩阵对应的最小特征值为0，其特征向量取值全为1 的向量。

### 2.3 conductance 有下界

对于最优分割标准 传导性 conductance 是有下界的。第二小特征值$\lambda_2$小于等于两倍的最优传导，也就是说**将为最优conductance的下界**。

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go5ajcf9okj20gg07gq37.jpg)

## 3 其他定义

- 拉普拉斯矩阵的第二小特征值也叫图的`代数连通度`. 因为当且仅当网络连通时， $\lambda_2$非零。如果网络不连通，此时，通常分别将网络的连通分支拿出来，分别应用算法。
- 拉普拉斯矩阵的最大特征值也叫图的`谱半径`.

## 总结

至此，算是介绍完对于图分割的思想以及关键结论的介绍。解释了为什么能根据图拉普拉斯矩阵的第二小特征值对应的特征向量对图进行分割。下面就是具体的分割算法，以及如何将二分推广到多分情况。敬请期待！

## 参考文章

1. https://blog.csdn.net/klcola/article/details/104800804
2. 图网络机器学习 | 社区发现 — 谱聚类算法
3. 斯坦福CS224W 图与机器学习5】Spectral Clustering
4. 谱聚类方法推导和对拉普拉斯矩阵的理解
5. https://linalg.apachecn.org/#/docs/chapter21
6. https://www.cnblogs.com/xingshansi/p/6702188.html?utm_source=itdadao&utm_medium=referral
7. http://www.cs.yale.edu/homes/spielman/sgta/SpectTut.pdf
8. http://www.math.ucsd.edu/~fan/wp/cheeger.pdf

