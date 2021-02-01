---
layout:     post
title:      CS224W-图神经网络 笔记2.1：Properties of Networks and Random Graph Models - 图的四大属性
subtitle:   Graph Machine Learning 
date:       2021-01-26
author:     HD
header-img: img/graph2.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记2.1：Properties of Networks and Random Graph Models - 图的四大属性

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整**
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/)
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=2)

## 1 引言

初步了解一个人，我们通常会问其身高、年龄、体重等信息。那么，要了解一个`图Graph`我们需要关注哪些信息呢？这就是本节讨论的内容。

## 2 图的四大属性

通常我们会从下面四个方面去初步理解一个图：

1. **度分布（Degree distribution）** $P(k)$
2. **路径长度（Path length）** $h$
3. **聚类系数（Clustering coefficient）** $C$
4. **连通分量（Connected components）** $s$

下面依此来看

### 2.1 度分布（Degree distribution）

定义：统计每个节点的度，然后计算不同的度数在所有节点中出现的频率。从图上看起来更直观。

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn6zs4i0ozj30pt092wf9.jpg)

### 2.2 路径长度（Path length）

需要了解三个概念：路径、距离、直径

- 1. `路径:`是一串彼此相连的节点组成的链。如:$P_n = {i_0, i_1, i_2,...,i_n}$, 注：一个条路径中的某点看可以出现多次！

- 2. `距离（Distance）`：两点之间的最短路径shortest path，称为`两点之间的距离`。**注：**

- 1. 对于有向图，一条路径的边一定要从左到右

- 2. 若两点之间不直接或间接相连则距离为无穷大.

- 如下图中的$X$和$A$点之间距离

![图片](https://mmbiz.qpic.cn/mmbiz_png/X0g5S5vasEMaztPDRKoyicTLAYiaa5aThygolbGVZnDONPHDHVVmozLR6FpEXe9sFgEuqDnqF83Gpu0NCT97vd6Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- `直径`:图的中任意两点之间**距离**的**最大值**，称为`图的直径`。

  但直径并不是很好用，考虑到一个`很扁的图`和`很圆的图`可能具有形同的直径。说白了就是容易受极端值影响。通常采用平均距离。

  - 计算时忽略不相连的两点间距离，因为无穷大。
  - 直径也适用于刻画图的某个连通分量（connected components）的直径。

- `平均距离`Average path length：所有两点之间距离加总，除以所有两点的组合数。

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn75r6s7ryj30m706pwer.jpg)

### 2.3 聚类系数（Clustering coefficient）

- 定义：是用来描述一个图中的某节点与其相连节点之间`聚集成团`的程度的一个系数。它只定义在无向图上。计算方法如下：

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn75ruefewj30q10an0u2.jpg)

**实际上就是算节点i与邻居构成实际组成的三角形数除上最大可能三角形个数**。翻译成人话就是 我的朋友之间相互认识情况。

- `平均聚类系数`：所有节点的聚类系数取平均就得到。

### 2.4 连通分量（Connected components）

- `连通分量`：图中的一个子图，子图中任意两点之间都存在路径，子图内节点和子图外的节点都没有路径。

- - 任何连通图的连通分量只有一个，即是其自身。
  - 非连通的无向图有多个连通分量。

> ##### 如何找到连通分量？
>
> 通过广度搜索算法（BFS）：
>
> - 随机选择一个节点作为起点，进行广度优先搜索；
> - 将广度优先搜索经过的节点进行标记；
> - 如果所有的节点都进行了标记，则该图是一个连通图；
> - 如果存在未标记的节点，从未标记的节点中随机选择一个节点作为起点进行广度优先搜索；重复第2步和第4步，直至所有节点都标记完毕；最后得到的多个连通子图中对的极大连通子图就是该图的连通分量。



## 3 MSN Messager网络

老师通过MSN（类似QQ的聊天软件）上一个月的对话活动构建的网络，这本身是个Multigraph 包含180M的用户（节点），两类边：是否好友、是否聊过天（可多次）。

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn75vwldnsj30s70fg0yi.jpg)

通过简化，将用户之间有过聊天简化成无向图。共180M节点，1.3B边。这个MSN网络的4大属性如下：

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gn75w6ach7j30j50fe0vx.jpg)

  **通过属性说明了什么呢？**

1. 度分布，均值14.4，说明平均每个人和14.4个人聊天
2. 聚类系数，均值0.11，平均每个人聊天过的朋友中，只有11%的朋友会彼此聊天。
3. 连通分量，最大联通分量覆盖99%的节点，`强连通`,绝大部分人都生活在一个大群体中。
4. 路径长度，均值6.6，平均两个人之间通过7个人就能聊上天;最大值30，意味任意两个人能认识最多通过30个人就能实现。

## 4 下文预告

**得到图的四个维度属性后，怎么评价这个网络呢？到底处在什么水平？**

就好像评价看一个人是高还是矮，我们是拿自己或他人作为参照，得出结论。那么对于网络我们也需要一个参照，这就是下面要谈的**随机网络模型**

## 5 参考文章

- http://web.stanford.edu/class/cs224w/slides/02-gnp-smallworld.pdf
- https://blog.csdn.net/Jenny_oxaza/article/details/106142668

