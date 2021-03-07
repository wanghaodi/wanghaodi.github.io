---
layout:     post
title:      CS224W-图神经网络 笔记6.2：Message Passing and Node Classification - 三类主要的节点分类算法介绍
subtitle:   Graph Machine Learning 
date:       2021-03-28
author:     HD
header-img: img/graph12.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记6.2：Message Passing and Node Classification - 三类主要的节点分类算法介绍

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整** 
>
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/) 
>
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=5)

[toc]

### 引言

前面，接着上文节点分类的协作分类算法思想介绍。这节具体看看细分的三类算法。

- `关系分类` relational classification
- `迭代分类` iterative classification
- `信念传播` belief propagation

### 1. 概率关系分类(Probabilistic Relational Classifier)

#### 1.1 算法过程

基本思想：**每个节点类别的概率是其邻接节点的加权平均**。过程如下：

1. 给已知和未知标签的节点分概率

2. - 未知的可初始化为`0`，或者其他先验的概率值

3. 随机选择节点更新其概率值为其邻居的类别概率的加权平均值。

4. - 直到达到收敛或最大迭代次数

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gob2eqmyxwj30h208xt9s.jpg)

**模型存在问题**：

- 模型不一定收敛；
- 该模型并没有使用到节点的特征信息；

**为什么采用随机选择节点？**

选择节点的顺序会影响最终结果，尤其是对于较小的图（较大的图对顺序不敏感）。从经验上看，随机选取在大多数情况下都达到较好的效果。

### 2. Iterative Classification

#### 2.1 算法过程

因为上述方法没有利用节点的特征，Iterative Classification 对这一点进行完善。整个过程分为两步：

1. `Bootstrap Phase`：

2. - 为每个节点分配一个向量.
   - 创建一分类器(local classifier）$f(a_i)$：使用节点自身特征，去预测每个节点的标签$Y_i$.；分类器可以是 SVM, kNN或者其他

3. `Iteration Phase`：

4. - 通过**计数、众数、占比、均值**等方式聚合邻居特征。并更新每个节点的特征向量 $a_i$。
   - 用分类器 预测并更新新的标签$Y_i$。
   - 重复过程直到标签稳定（收敛）或者达到最大迭代次数。

**模型存在问题**：模型不一定收敛；

### 3. Belief Propagation

信念传播（Belief Propagation）通过消息传递(passing message)的方式，解决`概率图模型`中的条件概率问题。这涉及了概率图的相关知识。算法将变量消去法中的求和操作看作一个消息传递过程，较好地解决了求解多个边际分布时的重复计算问题。

#### 3.1 什么是消息传递？

看到 Propagation，部分朋友也会联想到 深度学习训练中的正向传播和反向传播（back Propagation）。对于 信念传播（Belief Propagation）涉及信息传递（message passing）。对于图上的每个节点仅与它的**邻居**进行信息的收集（collect）和传递（distribute）。也就是当前节点的状态（state）不光取决于自身还与其邻居相关。在消息传递时，每个节点只能从其邻接接受消息。

借用课上的例子理解。**如何基于消息传递机制统计图的节点数？**或者说如何让图上每个节点都直到图中的节点数。

- 对于`链`图:通过向左向右分别进行消息传递，对于中间节点将左边传递的消息和和右边传递的消息的进行汇总，并加上自身的1，实现图上节点数的统计。
- 对于`树`图；通过加选中节点作为 根节点（root node），其余节点分别向根节点传递消息，最终根节点，汇总不同邻居传递的消息，并加上自身的1，实现图上节点数的统计。

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gob2j62x4wj30ls09pmxy.jpg)

当图上有环（loop/circle）时，传统的BP算法不适用，这时可以采用Loopy Belief Propagation。

#### 3.2 Loopy Belief Propagation

在阐述具体算法前需要，先做一些符号定义。（可将下面的状态替换为**标签**更好理解）。

1. `Label-Label potential matrix`$\psi$ ：其中$\psi(Y_i, Y_j)$表示节点$i$是类别$Y_i$的条件下，其邻接节点$j$为类别$Y_j$的概率;这反映的就是上面介绍的相邻节点间的`相关性correlation`。
2. `prior belief` $\phi$：$\phi_i(Y_i)$表示节点$i$为类别$Y_i$的先验概率；
3. `message`$m_{i->j}(Y_j)$ : 节点预测其邻接节点$j$为状态$Y_j$的概率
4. stats$\mathcal{L}$ : 表示所有的状态

有了上面的定义，可以得出节点$i$向其邻接节点$j$发送的消息为：

$m_{i->j}(Y_j)=\alpha\sum\psi(Y_i,Y_j)\phi_i(Y_i)\prod_{k\in{N_i\j}}m_{k->j}$

对$\forall\mathcal{L}$

整个过程如下：

![图片](https://tva1.sinaimg.cn/large/008eGmZEgy1gob2j6zukvj30oc0b4dhr.jpg)

**模型优点**：

- 实现简单，极易并行化；

- 普适性强，适用所有图模型；对于有环的图，也可以使用LBP算法，因为在实践中

- - a：环的回路较长，
  - b：环的某些边的相关性correlation较小

使得传播过程中其影响被削弱。

**模型存在问题**：模型不一定收敛；

#### 3.3 举例

课上老师给了一个将LBP用于在线拍卖网络的欺诈识别（NetProbe）。论文如下

> http://www.cs.cmu.edu/~christos/PUBLICATIONS/netprobe-www07.pdf

### 总结

说实话，对于BP算法的理解还很表面，希望后面通过数据和代码加深理解。

### 参考资料

1. https://zhuanlan.zhihu.com/p/279561894
2. [PRML]图模型推论(四)--循环信念传播
3. https://www.cnblogs.com/Leo_wl/p/3506242.html