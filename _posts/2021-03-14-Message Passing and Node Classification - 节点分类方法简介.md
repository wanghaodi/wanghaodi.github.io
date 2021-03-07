---
layout:     post
title:      CS224W-图神经网络 笔记6.1：Message Passing and Node Classification - 节点分类方法简介
subtitle:   Graph Machine Learning 
date:       2021-03-22
author:     HD
header-img: img/graph11.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记6.1：Message Passing and Node Classification - 节点分类方法简介

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整** 
>
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/) 
>
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=5)

[toc]

## 引言

**如何在给定一个网络上某些节点的标签，为所有其他无标签的节点分配标签？** 这类任务在现实生活中很常见。本节要介绍的协作分类(Collective Classification)是一种解决方案。

## 协作分类(Collective Classification)

协作分类(Collective Classification)是一种有效的网络数据分类方法,它利用网络数据中节点间的**关联关系**，结合部分节点标签和属性，推断未知标签的节点的标签。它是一种半监督的节点分类方法。

ps:其实从经验来看，当有标签节点数量足够时，应当优先考虑有监督的节点分类算法。

### 1 算法思想

#### 1.1 为什么可以利用关联关系，推断节点标签?

因为，网络中蕴含着`相关性信息`。

网络环境使得个体行为产生关联。具体相关性原因（形式）有三种：

1. `homophily`：“物以类聚，人以群分”，有相同性质的节点间可能存在更密切的联系.比如：有相同喜好的人，更倾向于产生联系。
2. `influence`：“近朱者赤，近墨者黑”，一个个体有可能会受其它个体的影响而具有某种性质。如因为朋友的推荐，也会产生相同的喜好
3. `confounding`：大环境可能会对个体性质和个体间的联系产生影响。可以看做非前两种原因的其他原因。![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gob23qnkt4j30la08et9w.jpg)

这三种形式，在现实网络的观察中都得到验证。如种族网络：

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gob25qmumkj30mj09qdgz.jpg)

#### 1.2 如何利用关联关系，推断节点标签?

##### Guilt-by-association

(我将其直译为 `连累`法).如果一个节点连接到具有特定标签的另一个节点，则该节点很可能有相同标签。**换句话说：相似的节点通常距离很近或者直接相连**。

基于这样的假设可以进行如区分恶意网站识别页等任务。因为通常恶意网页往往会相互链接，以提高知名度，使得看起来可信，以提高在搜索引擎中排名。

课上老师说，Guilt-by-association法要求 网络节点有同质性（homophily）。

##### 如何进行节点分类

**具体可利用信息有**![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gob260smerj30rz065q43.jpg)

但是，如果只使用这些信息，而不使用网络结构属性，那么我们只会在这些特征上训练简单的分类器。为了使我们能够进行集体分类，我们还需要考虑`网络拓扑结构`。这就是我们要介绍的协作分类（Collective Classification）。

### 2. 协作分类的步骤

需要以下三个组成部分：

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gob27zzb5aj30k80bk0uf.jpg)

1. `local classifier`：本地分类器

- 目的：预测初始标签：

  仅依据节点自身属性和特征，不涉及网络信息。这里可以用很多常见的分类器，甚至是kNN。其实，如果这一步效果足够好，是没有必要进行后续的操作，但通常有标签的数量非常少，这一步的效果，不会太好。

1. `Relational Classifier`：关系分类器

- 目的：基于网络捕捉节点相关性（如：homophily 和 influence）

  根据节点邻接的标签和特征预测该节点标签。运用了网络信息。

1. `collective inference`：协作推断

- 目的：基于网络传递相关性信息

  不想仅使用邻居的信息，而是希望通过多次迭代，能够获取其他节点的信息。

  具体的，是将关系分类器迭代运用到每个节点上，迭代直到收敛（节点与邻居的标签差异程度inconsistency最小）。另外，网络的结构会影响最终的预测结果。



### 2.1 协作分类的种类

它有三种代表性的近似推断的方法，都是迭代型算法。分别：

- `关系分类` relational classification
- `迭代分类` iterative classification
- `信念传播` belief propagation

近似推断有个前提假设——马尔可夫假设（Markov Assumption），即节点$i$的类别$Y_i$只取决于它的邻接节点$N_i$ . 用条件概率表示

$P(Y_i|i)=P(Y_i|N_i)$

##### 关于条件概率的精确推断和近似推断

从概率图模型角度看，如果我们将每个节点表示为**离散的随机变量**，其类成员的联合质量函数为$P$，则节点的**边沿分布**为所有其他节点上的$p$的总和。

- 精确推断（exact inferance） 精确推断 需要是节点数的指数级，是一个np-hard的问题。
- 近似推断（approximate inferance） 通过缩小传播范围（例如，仅邻居）和变量的数量，通过聚合来近似解决推断问题。并且大多数情况下可以取得不错的结果。

### 总结

各类方法的介绍放在下一节，同时结合具体案例，进一步加深理解，敬请期待。

### 参考文章

- https://snap-stanford.github.io/cs224w-notes/machine-learning-with-networks/message-passing-and-node-classification
- https://zhuanlan.zhihu.com/p/279561894
- https://blog.csdn.net/New2World/article/details/105410276