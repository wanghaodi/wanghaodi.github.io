---
layout:     post
title:      CS224W-图神经网络 笔记4.2：Community Structure in Networks - 网络中社区的挖掘算法——Louvain算法
subtitle:   Graph Machine Learning 
date:       2021-02-23
author:     HD
header-img: img/graph7.png
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记4.2：Community Structure in Networks - 网络中社区的挖掘算法——Louvain算法

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整**
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/)
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=5)

## 1 引言

通过前面的内容介绍，明白了网络中社区挖掘的意义和价值。那么怎么`自动的`挖掘网络中的社区呢？就是如果从左图到右图。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8b1b51awj30t00hcju8.jpg)

从图上可以看到社区的挖掘算法有很多种，大致可以分为两类：不重叠社区挖掘和重叠社区挖掘。考虑到篇幅，本文先介绍第一类——`不重叠社区挖掘`。

## 2 不重叠社区挖掘

### 2.1 评价指标

在介绍具体算法，我们先得定一个标准：**怎么评价我们的算法挖掘的社区好不好？**

从社区的定义我们知道，`社区（community）`：指具有大量内部边连接和很少外部边连接(到网络的其余部分)的`节点集`。简单说就是社区内部比外部连接更紧密。用公式表示就是我们的评价指标，模块度 Modularity。

顺带一提，你后续看到的 node groups,communities,modules, clusters 其实都是一个意思，统一叫社区。

### 2.2 模块度 Modularity

模块度 用来度量一个网络划分成社区的程度。它的思想是：

**一个好的社区一定是内部的连接要比`随机`连接情况下的连接更紧密。**

说到随机，那自然需要一个零模型（图模型），这里选择的是配置模型（configuration model），为了保证与原图有相同的度分布。类似于在介绍 motifs 时运用的模型，不同的是，这里允许有重边（multi edge）的 multigraph。

整个模块度的推导，可以查看下面的幻灯片：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8b40c7rjj30t50f9n03.jpg)

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8b5x3li1j30qe0ahabc.jpg)

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8b68oilij30ql0g4mz5.jpg)

- 模块度Q取值在 [-1, 1 ]
- 当社区内部边数大于预期边数时，模块度Q为正。
- 模块度Q在0.3 – 0.7 之间表示群落结构显著

结论：**模块度越大，挖掘的社区内部连接越紧密，效果越好。**

- **对无权图：模块度社区内部边的度数减去社区内节点的总度数。**
- **对于权重图：模块度也可以理解是社区内部边的权重减去所有与社区节点相连的边的权重和。**

既然，模块度是评价社区结构好坏的指标。那么，自然可以通过最大化模块度来识别社区。这就是下面算法的思想。

### 2.3 非重叠社区挖掘算法——Louvain

其实，直接最大化一个网络的模块度 是NP-hard 问题[Brandes, 2007]。但是，我们可以采用贪心算法近似求解，如：Louvain算法。

**初步介绍**

Louvain算法是检测社区的贪婪算法，它是应用非常广泛的社区挖掘算法。

#### 2.3.1 算法优点

1. 时间复杂度为O(nlogn)
2. 支持权重图
3. 提供分层的社区结果（hierarchical communities），可根据需要选择层级。
4. 适用于研究大型网络，因为它速度快，收敛快，高模块化输出。

#### 2.3.2 算法过程

每个阶段分两步：

- 第一步：**模块度优化**（Modularity Optimization）

  通过对局部的节点社区进行改变,优化模块度,计算模块度增益$\Delta Q$。

- - 将图中的每个节点放入相连的社区（每个社区一个节点）时的模块度变化值$\Delta Q$；
  - 将节点 i 移动到产生最大收益的节点 j 的社区。
  - 运行直到$\Delta Q$没有增益为止（局部最优）

- 第二步：**社区合并**（Community Aggregation）

  将识别出的社区聚合为`超级节点（super node）`，以构建新的网络

跳转到第一步，直至模块度没有提升,或者提升小于某个阈值。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8bf0wg5lj30u00h8gqf.jpg)

**注：在第一步计算时，选取节点的顺序对最终结果是有影响的，但不显著。**

从流程来看，该算法能够产生层次性的社区结构，其中计算耗时较多的是最底一层的社区划分，节点按社区压缩后，将大大缩小边和节点数目。

#### 2.3.3 改进策略

从模块度的计算公式入手

![截屏2021-02-01 下午8.42.21](https://tva1.sinaimg.cn/large/008eGmZEly1gn8bhq3j3gj30fm0283ys.jpg)

其中$\sum in$表示社区内的边的权重之和，$\sum tot$表示与社区内的节点相连的边的权重之和。计算每个点的模块度增益。

![截屏2021-02-01 下午8.43.28](https://tva1.sinaimg.cn/large/008eGmZEly1gn8biubpg6j30xu024aan.jpg)

简化后得到，便于计算的公式

![截屏2021-02-01 下午8.43.43](https://tva1.sinaimg.cn/large/008eGmZEly1gn8bj46eizj30cw028q35.jpg)

其实，上面的公式只反映了第一步中，考虑单独节点成社区的情况，此时只涉及到节点的添加。随着节点遍历，还会涉及节点，从先前合并后的社区中移除的模块度的变化部分。即：$\Delta Q = \Delta Q(i \rightarrow C) + \Delta Q (D \rightarrow i)$

具体课件如下：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8blglnhxj30u00df41r.jpg)

## 3 总结

根据论文，在实际挖掘时可以通过一些手段，进一步提升Louvin算法速度。如：

1. 设定模块度增益阈值，提前终止（类似于 early stopping）
2. 运算前移除度为1的节点，在社区挖掘结束后，再把其加入。

最后提下 louvain 算法的局限性：

1. 挖掘的社区大小，随着图的增大而增大。

   解决办法是通过图萃取，在运算前先对图进行处理，过滤不重要的边，具体见参考资料7

## 4 参考文章

1. https://arxiv.org/pdf/0803.0476.pdf
2. http://snap.stanford.edu/class/cs224w-2019/slides/
3. https://www.cs.cmu.edu/~ckingsf/bioinfo-lectures/modularity.pdf
4. https://www.cnblogs.com/fengfenggirl/p/louvain.html
5. GraphX实现：https://www.jianshu.com/p/460022a413c8
6. python实现：https://github.com/kevin-meng/python-louvain-modified/tree/master/community
7. 关联网络在度小满风控中的应用