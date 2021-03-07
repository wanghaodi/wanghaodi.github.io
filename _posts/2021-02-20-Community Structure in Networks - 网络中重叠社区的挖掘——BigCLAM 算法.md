---
layout:     post
title:      CS224W-图神经网络 笔记4.3：Community Structure in Networks - 网络中重叠社区的挖掘——BigCLAM 算法
subtitle:   Graph Machine Learning 
date:       2021-02-28
author:     HD
header-img: img/graph8.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记4.3：Community Structure in Networks - 网络中重叠社区的挖掘——BigCLAM 算法

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整**
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/)
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=5)

[toc]

## 1 引言

上面介绍了非重叠社区挖掘的Louvin算法，本节聊聊重叠社区的挖掘过程。两类社区区别可以反映在邻接矩阵上。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8y5a570sj30nm0e245t.jpg)

## 2 BigClAM 算法

### 2.1 主要思路

通过前面的介绍，基于图生成模型，可以生成一个网络。**相反的基于一个网络，能否确定一个图生成模型。并且模型自动划分节点的社区结构。**

听起来好像想的过分美好，但实际上是真实可行的。而这个特殊的图生成模型就是 AGM模型（Community-Affiliation Graph Model）。

**整个算法步骤分为三步**

1. 利用AGM(Community Affiliation Graph Model)构造一个图生成模型。
2. 根据我们实际的图G，迭代AGM的模型参数，寻找一个最优拟合（fitting）实际图的AGM。
3. 通过这个AGM，确定每个节点所属的社区

### 2.2 AGM模型（Community-Affiliation Graph Model）

模型是一个二部图结构。用表示.四个参数含义如下：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8yf43dfuj30ns0bx77n.jpg)

1. 社区 A 内部的每对节点相连的概率为$p_c$.每个社区对应一个。
2. 两个社区重叠的部分越多，则重叠区域的节点彼此相连的概率越高
3. 对于彼此之间没有共同社区的两个节点之间的连接概率并不为0，而为𝑷(𝒖,𝒗)=𝜺，叫做`背景社区`称为$\varepsilon - community$ 。论文中建议$(\varepsilon = 2|E|/(|V|(|V|-1)))$

虽然，AMG模型是在重叠社区挖掘的时候引入的，但并不意味着AMG模型只能生成有重叠社区的网络。事实上，其非常灵活，可以生成不同类型的社区结构。如

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8yj1ylpdj30u00bc77a.jpg)

## 3 图拟合

通过上面可以看到AGM的参数主要有三个：

1. 节点归属关系 Membership 𝑀
2. 社区内连接概率 $p_c$
3. 社区数 𝐶

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8yjy52fjj30h40afmy5.jpg)

### 3.1 确定M

对于归属关系M 用矩阵F(`Factor Matrix`)表示，矩阵的列为社区数，列为对应节点，每元素表示该节点归属社区的`强度`。从**强度到节点间连接概率**的推导过程如下图：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8ylz9wqrj30em0agtbe.jpg)

看到连乘和求大值，很自然会想到取对数，转化为求和,便于求导。优化采用`梯度下降`优化参数。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8ym9tz9tj30re0bu75x.jpg)

### 3.2 确定社区内连接概率$p_c$

从确定的节点强度举证就可以反推得到社区内部节点之间的连接概率。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8ymw1u2wj30ku03b0st.jpg)

### 3.3 确定社区数 𝐶

社区数，决定了因子矩阵F的列数。论文中采用的做法也非常巧妙，将其初始化一个较大的社区数 ，通过在优化函数上加入L1正则，让模型自己学习。类似逻辑回归中对于特征的选则。最终列不全为零的个数就是最终的社区数。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn8ynb8a6pj30ff0680sp.jpg)

通过最终的AGM模型可以很容易得到最终的社区结构。

## 4 总结

本算法是老师在2014年提出的算法（原论文为参考资料3），**其时间复杂度低，可以运用于大型网络数据。**另外一提，cs224w 每年老师讲的内容的详细程度不尽相同，建议大家结合起来一起看。

## 5 参考文章

1. http://snap.stanford.edu/agm/
2. http://snap.stanford.edu/class/cs224w-2019/slides/
3. http://ilpubs.stanford.edu:8090/1103/

