---
layout:     post
title:      CS224W-图神经网络 笔记2.2：Properties of Networks and Random Graph Models - 网络模型（Graph Model）
subtitle:   Graph Machine Learning 
date:       2021-01-31
author:     HD
header-img: img/graph3.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记2.2：Properties of Networks and Random Graph Models -网络模型（Graph Model）

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整**
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/)
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)

## 1 引言

学完前面两节之后，我们已经可以通过一些指标对网络，进行定量的分析。为什么在这里要插入一个网络模型？或者说，我们为什么要学习它？难道只是研究者之间的数学小游戏吗？

**所以，在一头扎进纷繁的图模型之前，我们需要先明白学习的目的和研究的意义！**

### 1.1 模型的价值

**源于预测的需要：模型是抽象的手段，预测才是目的**

前面老师也介绍了不同领域，不同系统的各类网络，它们是单案个体的分析，它们是否具有共性， 是否能通过参数化的模型去`模拟`，去`拟合`现实网络，以进一步研究现实网络的其他`未知`性质。

举个栗子，就像今年疫情期间，被大家熟知的传染病模型，根据理论推导的性质，`预测`爆发期，评估响应时间，提前防控风险。火神山、雷神山，国内的紧急动员都是在理论指导下，实际行动反映。 推荐大家看下毕导的科普视频。

> https://www.bilibili.com/video/BV1j7411z7KQ

### 1.2 图模型产生过程

通常一个图模型的诞生，有点像抽签。研究人员通过随便提个网络模型，分析网络模型的性质，然后拿真实网络对比验证，基于模型与现实之间的差异，不断修正模型到贴合现实情况。这个和机器学习模型训练机制很像，是个不断`迭代`和`精进`的过程。

下面老师介绍的三个模型的诞生和研究过程都是这个思路

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn767nkf1rj30hu05m749.jpg)

其实，网络模型研究至今，除了老师介绍的3个，还有很多模型，我找了些资料，整理了一个思维导图供大家参考。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn767yazyvj30mq0e376b.jpg)

说着这么多，下面进入具体理论部分。

## 2 图模型的分类

### 2.1 ES随机图模型

ES随机图模型（Erdös-Renyi Random Graph Model），用于生成无向图，它有两类：

1. $G_{np}$：$n$个节点的无向图，每条边以概率$p$独立随机(i.i.d)出现
2. $G_{nm}:$$n$个节点的无向图，$m$条边一均匀分布概率随机出现

课上老师重点介绍了第一类$G_{np}$

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn76cobmsvj30u0086myj.jpg)

下面研究下这类网络的四大属性情况:

#### 2.1.1 $G_{np}$的度分布（Degree distribution）

 $G_{np}$的度分布是二项分布（binomial）。

根据二项分布的性质，可以得到度平均为 $\overline{p}=p*(n-1)$

#### 2.1.2 $G_{np}$图的聚类系数（Clustering coefficient）

老师用公式推到，其实可以根据聚类系数定义：节点的邻居节点彼此相连的概率。 还记得$G_{np}$的前提假设吗？这个概率就等于任意两点之间相连的概率$p$。

这里有个前提，**当节点数$n->\infty$时，大图的平均度趋于一个常数**。 这和直觉也是一致的，通常社交网络中一个人的好友数量，并不会随着人口的增加而增加。 所以根据上面平均度的定义，就可以得到聚类系数$C=\frac{\overline{k}}{n-1}\approx\frac{\overline{k}}{n}$

#### 2.1.3 $G_{np}$图的连通分量

这里我调整了连通性和路径长度的顺序，更利于路径长度的理解。这里课上老师跳过了一页PPT。

随着概率的不断增大，图的连接情况，会发生变换。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn76pxt98nj30pk06pjrt.jpg)

老师做了个实验，对于10万个节点的图，$k=p(n-1)$从0.5到3对于巨分支的节点占比情况。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn76r37vm3j30pb0c5q6f.jpg)

可见，**当平均度$k=p(n-1)>1$时，巨分支就开始出现**。这个结论很重要！

#### 2.1.4 $G_{np}$图的路径长度（Path Length）和直径

老师先引入了一个定义`扩展系数 Expension`$\alpha$,公式如下：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn76tqzhfrj30ni0aw75i.jpg)

它描述了图的任意节点集与剩余节点之间边的数量。可以理解为**任取图中节点的一个子集，下一步（step）处达的最小节点数目**。

- **（平均）路径长度**

基于`expension`定义,老师给出路径长度为$O((logn)/\alpha)$。说实话，具体的证明，我不会，网上也没有找到。但是以`二叉树`举例，这个路径长度是**对数**$log(n)$还是好理解的。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn76xbibr9j30k905ijs6.jpg)

- **直径（Diameter ）**其实这一块，单独理解有点困难。结合上面关于连通分量的介绍： 当$k=p(n-1)>1$时，随机图中出现巨分支，而网络的形态取决于巨分支的形态。 随机图在$logn>np>c$,$c$为常数条件下，保证巨分支的出现，此时直径为 $diam(G_{np}=O(logn/log(np)))$. 相关证明在参考[资料4](http://cseweb.ucsd.edu/~fan/research/papers/diameter.pdf)可以查看（22页）。

另外，网络有巨分支存在，自然广度搜索算法（BFS）运算是对数复杂度$O(logn)$. 

#### 2.1.5 对比现实模型

通过上面大篇幅的模型介绍，那么真实情况随机图模型$G_{np}$对于现实的拟合情况如何呢？

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn771rwit3j30qg0crta3.jpg)

可以看到：

- 优点

- - 巨分支所占比例是接近的

  - 平均路径长度也比较接近。

    这点是最有趣的发现：原先在实际网络（MSN）分析的时候，我们对于6度理论以及小世界效应非常惊讶。但实际上不足为奇。通过这个简单的随机图模型，就能做到。（**平平无奇**）

- 不足

- - 度分布差异较大。 现实网络呈现幂律分布，而随机图呈现泊松分布（二项分布）
  - **聚类系数相差极大。**对于这一属性，这才是应该更关注。

总结一下，随机图模型$G_{np}$很粗糙，对于现实网络的拟合并不好。但是，并不是学习它没有意义，它是后续随机图模型的“迭代”基础。算是**地基工程！**

### 2.2 小世界网络模型 The Small-world model

- `原因`：为了解决前面随机图，对于现实网络的聚类系数的严重低估问题。

- `目标`：生成一个有较短的平均路径长度又具有较高的聚类系数的随机图

- `方法`：随机重连，由[Watts-Strogatz ‘98]提出

- - 从一个环状的规则网络（regular attic ）开始，网络含有N个结点，每个节点向它左边最近的K个节点连出K条边$K\ge2$，并满足$N>>K>>ln(N)>>1$。
  - **随机化重连**：以概率p随机地重新连接网络中的每个边，即将边的一个端点保持不变，而另一个端点取为网络中随机选择的一个节点。规定，图无重边和自环（self loop）。
  - 改变p 值可以实现从规则网络(p = 0 )向随机网络(p = 1)转变。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn776vz24ij30n70fd408.jpg)

从实验结果来看，通过较小的重连概率p就可以实现这一目标预期的目标。给个小世界模型的定义：

> **小世界网络模型**是一类具有较短的平均路径长度又具有较高的聚类系数的网络的总称。

总结下小世界模型：

优点

- 提供了聚类系数与路径长度（小世界效应）之间的联系
- 让随机图模型在聚类系数方面拟合了现实网络。

不足

- 对现实网络节点度的幂律分布这一特点，不能很好的拟合

说明还有进一步提升的空间。这就是下一个要介绍的随机图模型。

### 2.3 随机Kronecker图模型（Kronecker Graph Model）

这时候轮到随机Kronecker图模型出场了，它可以解决最后的度分布的问题。

- `原因`：为了解决随机图度分布与现实网络度分布差异问题。
- `方法`：采用递归方法

通过`Kronecker乘积`来递归来生成网络，Kronecker乘积就是生成自相似矩阵的一种方式.

#### 2.3.1 基础知识

- `自相似性（Self-similarity）`： 物体总是和自身的某些局部是相似的。
- `克罗内克积（Kronecker product）`：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn778xh5m1j30j60ag75c.jpg)

如果初始的$K_1$矩阵是这样的0-1邻接矩阵。那么3阶的Kronecker图是最右边的样子。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn779f6e77j30jz06j3zg.jpg)

这么做虽然可以生成图，但有个问题！当初始$K_1$矩阵和阶数确定了最终的结果也就确定了。**不够灵活，也不能解决度分布的问题**。

#### 2.3.2 随机Kronecker图

为了解决上面的问题，引入随机性，将初始矩阵$K_1$由无权的邻接矩阵，改为带权的权重（概率）矩阵。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn77auqvusj30k8071dgi.jpg)

这虽然可以实现随机性的目标，但是也有个问题——`计算量太大！运算慢！`,它需要计算$N*N$次，如果是一百万个节点，将要计算$1*10^{14}$次！需要一种更快速的方法。

#### 2.3.3 快速生成随机Kronecker图

- `思想`：优先生成概率最高的边。
- `方法`：递归逐层向下，优先计算概率最大的边。 具体后面结合代码介绍。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn77c29vflj30hq05lt9o.jpg)

通常生成随机图的前，我们有着预定的节点数和边数。通过这种方法生成随机图恰好可以满足我们的需求，免去很多无效计算。

#### 2.3.4 模型与现实对比

课上，老师给出了对应的实验结果，可以看到随机Kronecker图模型在多个维度很好的**拟合**了现实网络。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn77dd6n0jj30po0fkq85.jpg)

### 3 总结

至此，完成了随机图模型对于现实网络的拟合，有了可以更好的分析和预测现实网络的工具。

这节课内容很多，不少新的概念和公式，期间查了不少资料，用了不少时间消化，好在基本理解了！

### 4 参考文章

- http://web.stanford.edu/class/cs224w/slides/02-gnp-smallworld.pdf
- 《网络科学引论》郭世泽 陈哲译
- https://blog.csdn.net/Jenny_oxaza/article/details/106142668
- http://cseweb.ucsd.edu/~fan/research/papers/diameter.pdf
- https://www.geeksforgeeks.org/erdos-renyl-model-generating-random-graphs/
- https://snap-stanford.github.io/cs224w-notes/preliminaries/measuring-networks-random-graphs