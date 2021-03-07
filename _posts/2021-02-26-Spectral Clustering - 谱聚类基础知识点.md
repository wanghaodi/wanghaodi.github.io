---
layout:     post
title:      CS224W-图神经网络 笔记5.1：Spectral Clustering - 谱聚类基础知识点
subtitle:   Graph Machine Learning 
date:       2021-03-06
author:     HD
header-img: img/graph9.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记5.1：Spectral Clustering - 谱聚类基础知识点

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整** 
>
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/) 
>
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=5)

[toc]

## 1 引言

**本节从矩阵计算和线性代数角度来分析图。**而相关矩阵包括：邻接矩阵和图拉普拉斯矩阵。在进入具体谱聚类算法介绍前，有必要先熟悉下相关矩阵、特征值和特征向量等相关知识。

### 1.1 线性代数矩阵知识

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go4tnj883uj20r70eyq4i.jpg)

![image-20210307095316917](https://tva1.sinaimg.cn/large/008eGmZEgy1gob3st0ohsj30qx09eq43.jpg)

### 1.2 图的矩阵表示

先复习下图的矩阵表示形式。对于`无向图` $G=(V, E)$，与之相关的矩阵表示有如下三种：

1. 邻接矩阵A
2. 度矩阵D
3. 拉普拉斯矩阵L=D-A

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go4ttt1woqj20om0f1dhj.jpg)

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go4tv0gmcij20o20ecdhe.jpg)

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go4tvy2alyj20od0ff766.jpg)

其中拉普拉斯矩阵的性质，是由其实对称举证性质决定的。其中有一点，**关于特征值大于等于0（半正定）的**证明如下：

![图片](https://tva1.sinaimg.cn/large/e6c9d24egy1go4twqpwsrj20qf0drmyv.jpg)

来源：

http://www.sofasofa.io/forum_main_post.php?postid=1004484

## 2 一些概念

下面，补充些关于谱图相关的概念。

### 2.1 什么是谱（Spectral）

谱的定义：谱就是指矩阵特征值的集合。该名称来自光谱，指一些纯事物的集合，就像将矩阵分解成为特征值与特征向量。定义`谱半径`为该方阵最大的特征值。

在谱图里面实际上矩阵指拉普拉斯矩阵 ，对它的`特征值`和`特征向量`的分解称为`谱分解`.（特征分解，对角化，谱分解是一个概念）

##### 谱图理论（Spectral graph partitioning）

图谱论是指分析图G的矩阵表示的“`频谱`”。频谱是指由其对应的`特征值`的大小升序排序的一组图的`特征向量`：

- https://zhuanlan.zhihu.com/p/81502804

### 2.2 图拉普拉斯矩阵的由来

整个谱图理论都是围绕着图的拉普拉斯矩阵为核心进行展开的，那么为什么将其定义为D-W呢？它其实是拉普拉斯算子在图上的推广，它是离散的拉普拉斯算子。

其第 i 行其实是第 i 个节点在产生扰动时对其他节点产生的收益累积。

具体可以看下面的链接里的公式推导：

- https://zhuanlan.zhihu.com/p/84649941

### 2.3 拉普拉斯矩阵和拉普拉斯算子之间关系？

**拉普拉斯矩阵是离散的拉普拉斯算子**。具体分析参考下文

- https://zhuanlan.zhihu.com/p/85287578

### 2.4 拉普拉斯算子的物理含义？

根据定义，函数的拉普拉斯算子$\nabla^2f$又可以写成$\nabla \cdot \nabla f$ ，其被定义为函数 梯度的`散度`。

**拉普拉斯算子实际上衡量了在空间中的每一点处，该函数梯度（向量场）是倾向于增加还是减少.**

- https://zhuanlan.zhihu.com/p/67336297

## 3 图划分

### 3.1 图划分与社区发现之间联系与区别

##### 联系

图划分（graph partitioning）与社区发现（community detection):二者都是根据网络中的边的连接模式，把网络中的顶点划分成群组、簇或者社区。使得同一群组间节点紧密连接，而不同群组间只有少数的边。

##### 区别

- 图划分得到的`群组的数量`基本是确定的，而社区发现是不确定的。
- 另外，从`目的角度`看，前者的目的通常是将网络划分为更多更小的块，为了划分而划分。没有好的划分，也要尽量在不好的划分中选择一种。而社区发现则是为了了解网络结构，没有符合条件的划分可以不划分。

这小节，算是铺垫。具体图划分以及谱聚类放到下一小结，再做讨论。

## 4 参考文章

1. GNN教程：第六篇Spectral算法细节详解！
2. https://zhuanlan.zhihu.com/p/85287578
3. https://zhuanlan.zhihu.com/p/84649941
4. https://zhuanlan.zhihu.com/p/81502804
5. https://linalg.apachecn.org/#/docs/chapter21
6. https://www.cnblogs.com/xingshansi/p/6702188.html?utm_source=itdadao&utm_medium=referral
7. http://www.cs.yale.edu/homes/spielman/sgta/SpectTut.pdf
8. http://www.math.ucsd.edu/~fan/wp/cheeger.pdf