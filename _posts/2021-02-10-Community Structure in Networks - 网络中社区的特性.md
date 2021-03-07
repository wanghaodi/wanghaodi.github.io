---
layout:     post
title:      CS224W-图神经网络 笔记4.1：Community Structure in Networks - 网络中社区的特性
subtitle:   Graph Machine Learning 
date:       2021-02-18
author:     HD
header-img: img/graph6.png
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记4.1：Community Structure in Networks - 网络中社区的特性

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整**
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/)
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=5)

[toc]

## 1 引言

本节，老师前半段部分主要是基于 Mark Granovetter 教授60年代的博士论文展开的。介绍当初的猜想，如何在后续研究过程中被逐步验证和接受的，很有意思。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn83hoi9f4j30fb0a7aa8.jpg)

## 2 定义

- `社区（community）`：指具有大量内部边连接和很少外部边连接(到网络的其余部分)的`节点集`

### 2.1 为什么要研究图上的社区（community）

在回答具体问题前，不妨先看个有趣的社会学研究案例：

*Mark Granovetter 教授在他的博士论文中有做过这样一项研究，他研究人们怎么获取新的工作信息，是怎样找到自己的工作的。他发现，**人们通常更倾向于通过熟人（acquaintances）获取这些信息，而不是通过联系更加亲密的朋友（close friends）**。这是一个比较“反常”的结论，因为在我们的印象中，我们总是觉得自己在遇到困难或事情的时候，会找更亲密的人来帮忙。*

> 注：在英文中，acquaintance的意思是a person that you know but who is not a close friend，不会经常联系，关系上看应该要比close friends要疏远一点。close friend指每天都联系的意思。

#### 2.1.2 如何解释上述现象

教授的解释：

1. 结构角度

**边有强弱之分，它们在网络结构上和网络信息传递上起到的作用也是不同的。**亲密的朋友边强度较强，而距离较远的熟人边强度较弱。

1. 信息角度

**信息在网络中是流动的（flow of information）**，亲密的朋友提供的信息（边）是冗余的，即你知道的我也知道。而关系较远的熟人，可以提供的信息更多新价值。

这是不是也说明了 **村里要通网的重要性！**

这里回答上面的问题：

 **为什么要研究网络中的社区？**

- 在现实网络存在社区结构，反映了结构的紧密程度。
- 能解释信息的传播。

总之很有用！它能用来分析解决很多问题。学吧！

## 3 一些重要概念

在进行定量分析前，需要先熟悉几个概念，都是为了衡量社区紧密程度做的铺垫：

1. `三元闭包(triadic closure)`：彼此相连的3个节点和对应边构成的子图。

2. - 更多的三元闭包 = 高聚类系数.
   - 有共同邻居的两点，更大概率相连。

3. `边的重叠度（Edge Overlap）`

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn84os5qevj30iz0980uj.jpg)

说人话就是，两点的共同邻居在它们所有邻居中的占比。反映关系的强度（stength）。这点通过下面的电话网络的实证研究中得到验证。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn84qmf0yej30q109amy1.jpg)

3. `捷径（local bridge）`当$O_{ij}=0$相邻两点的共同邻居为0时，这条边叫做捷径。

## 4 真实的网络案例

老师举了个电话网络（mobile call graph）的例子，来定量分析社区的一些特性。

### 4.1 边的重叠度（overlap）与边的强度（strength）关系

- **正相关关系**

即边的重叠度(通讯录中有共同好友的)越高，边的强度（彼此打电话的概率）越高，如下图左边蓝色线。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn84shzrjqj30u00ah0un.jpg)

从实际网络上可以看到右图，如果用电话次数多少代表边的粗细。可以看到真实网络中，连接紧密的两点边越粗（通话越多）。明显区别于随机网络随机分配权重情况。

### 4.2 边的强度（strength）与网络结构之间关系

- **低强度的边对最大连通分量的大小影响更大**

因为边的overlap 和 stength 是正相关关系。所以，从图上可以看出来，

- 先移除低overlap的边，对于网络的最大连通分量的影响`大于`先移除高overlap边（左）
- 先移除低stength的边，对于网络的最大连通分量的影响`大于`先移除高strength边（右）

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn84ttn9akj30tw0c5wi2.jpg)

## 5 总结

以上内容，解决了网络中社区是什么和为什么两个问题。下面就是怎么办的问题，**怎么从网络中挖掘社区?**

本节的大部分内容都是参考下面链接1，作者总结的非常到位，学习前读两遍很有收获，在此表示感谢!

## 6 参考文章

- https://blog.csdn.net/Jenny_oxaza/article/details/106267686
- http://snap.stanford.edu/class/cs224w-2019/slides/

