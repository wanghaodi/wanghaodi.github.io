---
layout:     post
title:      CS224W-图神经网络 笔记1 ：Introduction ：Structure of Graphs
subtitle:   Graph Machine Learning 
date:       2021-01-23
author:     HD
header-img: img/graph1.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记1：Introduction : Structure of Graphs

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整**
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/)
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=1)

[toc]

## 1 引言

这篇是CS224W 图机器学习笔记的第一篇，本笔记主要梳理课程的关键点，以及一些图相关的基本概念。

### 1.1 为什么要研究网络

1. 图/网络 无处不在。
2. 跨领域。图在不同研究领域都有应用，如计算机科学、社会学、经济学等等。
3. 数据丰富。
4. 有前景。社交网络分析，药物开发，AI推理等

### 1.2 网络分析主要研究方向（场景）

- **对节点的类型/属性进行预测**。如：节点分类。
- **预测两个节点是否相连**。如：链路预测（link prediction）。
- **识别紧密相连的节点群**。例如：社区挖掘（Community detection），节点聚类。
- **计算两个节点或者网络的相似性** 。node embedding / graph embedding

老师在课程中ppt中给了很多现实例子。**总之，学了很有用就对了！！**

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn6z1dw0plj30u00lcn03.jpg)

## 2 图的基础知识

为了研究这些，需要有`系统(system) -> 图(Graph)`的建模的过程。

先需要一些网络/图论基本知识

### 2.1 图的定义

> - A network is a collection of objects where some pairs of objects are connected by links
> - 网络是互连成对的节点的集合。

网络是一种描述复杂系统中实体关联的通用语言。是一种通用的数据结构。它表示了成分之间的联系.

![图片](https://mmbiz.qpic.cn/mmbiz_png/X0g5S5vasENqTicXUibhicdvRnPic2YrmC5LGVfnOIesOaBIs2eECk1A6icFsXGicibU9kH4OichPAOVoVWIodcSypdMyg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图的数据表示`G(N,E)` G是图，V是节点，E是边。上面既有图，又有网络，那它们之间的区别是什么呢？

### 2.2 网络(Network)与图(Graph)之间区别

- Network：表示现实世界的系统。举例：互联网（Web）、社交网络（ Social network ）等 对应描述：网络（Network）, 点（node）, 边（link）
- Graph：是 Network 的数学表示。举例：、社交图（ Social graph）、知识图谱（ Knowledge Graph ）等 对应描述：图（Graph）,点（ vertex）, 边（edge）

很多时候，并不太严格区分。

### 2.3 网络的粗分类（不严格）

网络大致可分为两类，有时它们的界线并不那么明显。

- Networks (Natural Graphs)

第一类可以看做是自然网络，比如社会网络、社交网络、蛋白质图谱、基因图谱等。

- Information Graphs

第二类就是各种信息汇聚成为的网络，如知识图谱，相似网络（similarity netoworks）等等。如基于地址相似度构建的地址网路。

### 2.4 图论中图的分类

图的分类：

#### **2.4.1 根据边的方向**

- 1. **无向图**，如：同事关系，论文合作关系
  2. **有向图**，如：B站用户关注与被关注关系

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn6z5cr9guj30u00awdh6.jpg)

补充概念——`度(degree)`

**定义**：连接节点i的边的数量，称为节点i的`度(数)`。

上左图的无向图中节点a的度为4.

对于有向图，节点i的度分为

- `入度in-degree`：指向自身边的边的数量。上图c点的入度是2

- `出度out-degree`：指向其他节点边的数量.c点的出度是1

  

`平均度 avg degree`:衡量图的稠密性。等于所有节点度的平均。

从图的邻接矩阵来看

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn6z625z9pj30u00g4tbn.jpg)

#### **2.4.2 根据边是否有权重**

1. **无权图**，权重为1**
2. **加权图**

#### **2.4.3  根据节点连接方式**

```
- 补充概念——二部图的折叠 （Projection/Folded）
  也就是将异质的二部图图，变成同质图。
```

- 1. **二部图**（Bipartite graph）：图可分为两类内部不相连的节点集。如：作者和论文构成的图，电影和演员构成的图以及用户和商品之间构成的图等等。

- **![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn6z6op63bj30s10fjgqh.jpg)**

- 2. **完全图**（complete graph）。任意两点都有边相连
  3. **自环图**（Self-edges (self-loops)）。自己与自己相连。邻居矩阵的对角线不为0.
  4. **多重图** （ Multigraph ）。存在两点之间大于一条边。

- ![图片](https://mmbiz.qpic.cn/mmbiz_png/X0g5S5vasENqTicXUibhicdvRnPic2YrmC5LibKyvIJ37Mbno0hwXoDvOELlSyu2TLLRxHmdPxtUMUJ48ZyFhdUmKhg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

  **现实中系统繁多，数学中的图很多，因此，正确建模很关键！！**

### 2.3 图数学的表示

这部分在代码篇再详细展示。这里只做简单介绍。虽然课程老师推荐使用官方的SNAP包，进行实操。但是因为之前主要用的还是`networkx`，为了降低学习成本。动手部分就采用 networkx了，这些部分代码，可以在下面的github链接获得。

> https://github.com/wanghaodi/Graph_ML

- **邻接矩阵**（Adjacency matrix） 现实中大部分的网络的邻接矩阵是非常稀疏的，通常采用稀疏矩阵or邻接列表存储。

- **邻接列表**（Adjacency list）

- **边列表**（Edge list）

- - 如 [(1,4),(2,1),(4,2),(4,3)]

### 2.4 图的连通性

#### 2.4.1 对于无向图

- **连通图（无向图）**：任意两节点间存在直接或间接的链路关系。子概念：

- - **Bridge edge**: 去掉该边，图变成菲连通图。下图中绿色边
  - **Articulation node**：去掉该点，，图变成菲连通图。下图中蓝色节点

- ![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn6zhel5tuj30tx0dhdge.jpg)

#### 2.4.2 对于有向图

- **强连通有向图**：图内部对于任意两个节点，存在路径，彼此之间相互触达。

- **弱连通有向图**：忽略方向时才连通的图

- **强连通分量**（Strongly connected components (SCCs)）

  子图内部任意两个节点，存在路径，彼此之间相互触达

最后放一张，现实中的不同系统中网络的统计信息再次，说明网络的稀疏性！

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn6zhxlrfvj30u00bbq6u.jpg)

## 3 参考文章

- http://web.stanford.edu/class/cs224w/slides/01-intro.pdf
- https://blog.csdn.net/Jenny_oxaza/article/details/106114249

