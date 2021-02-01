---
layout:     post
title:      CS224W-图神经网络 笔记3.1：Motifs and Structural Roles in Networks - 网络的结构（Motifs and Graphlet）
subtitle:   Graph Machine Learning 
date:       2021-02-08
author:     HD
header-img: img/graph4.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记3.1：Motifs and Structural Roles in Networks - 网络的结构（Motifs and Graphlet）

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整**
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/)
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=4)

[toc]

## 1 引言

前面两节，讨论的网络的整体统计信息，这一节开始聚焦网络中的一些特殊结构（子图）和其中节点的的角色。

## 2 一些新概念

在深入学习本节前，需要先理解几个关键概念。

- 子图/子网络（Subgraph/Subnetwork）
- motifs
- graphlet
- （节点的）结构性角色（structural rols）

### 2.1 子图Subgraph/子网络/Subnetwork

**定义**：字面上就可以理解，就是网络中的一部分节点和它们之间的边。

**重要性（why）**：我们可以借助子图挖掘出图的一部分性质和信息。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7wlhnemzj30p604hdgf.jpg)

例如，对不同类型的网络统计三个节点的各类子图出现的频次，得到不同网络的重要性概览（Network significance profile）。同类网络，有相似的子图分布。有的子图低于平均，有的高于平均。顺带一提高于平均的是下面要介绍的motifs。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7wmd2oxpj30kv0csdha.jpg)

### 2.2 motifs

**定义：**（what）一类特殊子图的统称，它具有如下特点:

- pattern：小的诱导子图（**Small induced subgraph**）。

- - `诱导induced` 表示节点之间的连接都包含在内。

- recurring：**高频出现**

- significant：**重要**指比预想（随机图）中出现的频率更高。

**其他特点：**

- 同一类motifs 之间，诱导子图的边必须**完全一致**。多一条边改个方向都不行
- 不同 motifs 之间可以重叠。

**重要性（why）**

- 帮助我们理解网络，理解不同节点之间关系。描述了节点间交互模式，通过模式匹配去理解网络。

**如何衡量重要性（how）**

因为Motifs 的定义要求Motif出现频率要更高，更重要。因此可以通过与`随机网络`中的Motifs数进行对比，以衡量真实网络中一种子图的显著性。具体通过下面的公式进行：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7wqla7fgj30rs0bhdhv.jpg)

因为，通常更大规模的网络有更高的Z值。因此，为了更方便在不同规模的网络之间进行比较，通过标准化之后的Z值的向量SP的方式解决。如上图所示。

**关键问题 —— 随机网络怎么生成？**

- 配置模型：根据给定的度序列k_1, k_2, …, k_N生成随机图，用来与真实网络进行对比。通常称为零模型（null model）

- 生成配置模型的两种方式：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7ws99swhj30oc0bw3zg.jpg)

- - **随机连接：** 该方法生成的随机图，因为会忽略重边和自连接，故同一节点的度会发生改变。但根据《网络科学引论》的p275 。**当网络规模足够大时，网络中的自边和重边的平均数将会趋于常数**。

- - **随机交换：** 随机选择一对边，然后重连两个边，交叉两个点。生的随机图的节点的度，不发生改变。但计算的代价会较高，运行慢。为了保证随机图的随机性，需要运行的次数为 **Q \* E** 次，其中Q应尽可能的大，如100。

获取具有相同节点数，边数，节点度数的随机图之后，我们就可以计算子图$i$的$Z$值。**高值说明该子图是图G的一个Motif。**

将一组子图的Z值作为网络的特征向量$SP$，我们就得到了上面展示的对比图1。

### 2.3 Graphlets

- 非同构子图单元，是一类特殊的子图。Graphlets是对motif的扩展。它与motifs的区别：

  - motif是从`全局`的角度来描述`图`的。用不同motifs来构成一个图的向量表示。

  - 而Graphlet是从`局部(节点)`的角度出发来描述`节点`。用不同graphlet中的节点相对位置（局部信息），来形成一个节点的向量表示。

![截屏2021-02-01 下午3.27.39](https://tva1.sinaimg.cn/large/008eGmZEly1gn82e9ffbbj31cr0u044z.jpg)

![截屏2021-02-01 下午3.27.17](https://tva1.sinaimg.cn/large/008eGmZEly1gn82dxv4pfj31aq0t2tn3.jpg)

![截屏2021-02-01 下午3.36.52](https://tva1.sinaimg.cn/large/008eGmZEly1gn82nujt72j31cu0tkdor.jpg)



#### 2.3.1 同构图 （isomorphic graph）

可以参考知乎上的解释： [怎么理解图的同构?怎么判断两个图是否同构？ - 少文的回答 - 知乎](https://www.zhihu.com/question/326620873/answer/1063169941)

这里给出图论上的定义：

> 在图论中，假设G=(V,E)和G1=(V1，E1)是两个图，如果存在一个双射m：V→V1，使得对所有的$x, y \in V$均有$x, y \in E$等价于$m(x)m(y) \in E_1$，则称G和G1是**同构**的。

简单的说，两个同构图，节点和边一致，且存在一个一一映射使得每个节点相互对应。

#### 2.3.2 非同构子图集

不同节点数的子图可以构成的非同构子图数量不同，节点越多，非同构子图数量呈指数增加。如下图， 可以看到，不同颜色的点，代表相对位置不同类型的点。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7x0zpr7yj30je0ah0wi.jpg)

#### 2.3.3 Graphlet degree vector（GDV）

通过计算一个节点所在的Graphlets中不同的非对称位置，可以对节点附近的**局部结构**进行衡量。

GDV的定义：一个节点所在位置的**频率**组成的向量。

### 2.4 如何获得motifs和graphles（how）

可将问题拆解为两步：

- 1.枚举所有大小为k的子图。

- 2.计算这些子图出现的次数。

- - 这里涉及子图同构的判断，是一个 NP-complete问题，计算困难。通常，子图的大小选择在 3到8个点。

**第一步：Extract Subgraph Enumeration(ESU)**

为了枚举所有大小为k的子图，老师介绍了ESU算法。ESU算法[Wernicke 2006]中的两个集合：

- $V_{subgrapg}$ : 目前已经构造的子图
- $V_{extension}$ : 用于扩展子图的候选节点集合

**算法思想**：每个节点分配唯一序号，从一个节点 开始，添加符合以下性质的节点 到：

-  $u$的节点编号必须大于$v$
-  $u$只能是某个新加入的节点$w$的邻居，不能是任何$V_{subgrapg}$中的节点的邻居

$ESU$算法是一个递归算法，运行过程呈现为一个深度为 k 的树，被称作`ESU-tree`。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7x6oddt4j30t3092q6m.jpg)

**第二步：Extract Subgraph Enumeration(ESU)**

为了计算这些子图出现的次数，因为涉及到如何判断图与图之间`是否同构`，可使用 McKay’s nauty 算法 [McKay 1981]。

**即若图G中任意一对邻接的节点 u 和 v ，在图H中都有f(u)和f(v)邻接，则图G和图H同构。**

n个节点的两个同构图判断，需要$n!$次计算，计算量很大。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7x7k9tocj30mq0a174l.jpg)

通过上面两步我们可以得到图的 motifs 和 graphlet和对应GDV。

![截屏2021-02-01 下午3.47.00](https://tva1.sinaimg.cn/large/008eGmZEly1gn82ygnxyjj31bk0s8dmg.jpg)

## 3 总结

本节，为了研究网络的结构特性，通过定义了motif 和 graphlet 两类子图，从不同角度对图的拓扑性质进行了研究。

其中，GDV 算是早期node embedding的一种。

![截屏2021-02-01 下午3.30.20](https://tva1.sinaimg.cn/large/008eGmZEly1gn82h32tvgj31co0r8af4.jpg)

## 4 参考文章

- https://blog.csdn.net/lssx0817/article/details/106195822
- 《网络科学引论》郭世泽 陈哲译
- https://snap-stanford.github.io/cs224w-notes/preliminaries/motifs-and-structral-roles_lecture

