---
layout:     post
title:      CS224W-图神经网络 笔记3.2：Motifs and Structural Roles in Networks - 网络的结构（Structural Roles）
subtitle:   Graph Machine Learning 
date:       2021-02-08
author:     HD
header-img: img/graph4.jpg
catalog: true
tags:
    - Graph_Machine_Learning
    - CS224W
---

# CS224W-图神经网络 笔记3.2：Motifs and Structural Roles in Networks - 网络的结构（Structural Roles）

> **本文总结之日CS224W Winter 2021只更新到了第四节，所以下文会参考2021年课程的PPT并结合2019年秋季课程进行总结以求内容完整**
>
> 课程主页：[CS224W: Machine Learning with Graphs](http://web.stanford.edu/class/cs224w/)
>
> 视频链接：[【斯坦福】CS224W：图机器学习( 中英字幕 | 2019秋)](https://www.bilibili.com/video/BV1Vg4y1z7Nf?p=4)

## 1 引言

工作中， 我们因为所处的岗位不同，公司中扮演的角色（Role）也不尽相同。而在网络中，节点的拓扑结构，决定了节点的角色也是不一样的。这就是本节要研究的内容——**Structural Roles**。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7xnl4wqkj30o20dpjxm.jpg)

## 2 一些新概念（what）

- 1. `角色`（Role）：具有相似结构特征的节点集合，不要求彼此相连。

- 2. `社区`（communities ）：结构上，内部连通，内部可达的子图。

角色（Roles） 和 社区（communities ）之间还是有明显区别。两者概念上是互补。

例如，对于计算机学院的社交网络而言：

- 角色Roles：有教员、工作人员、学生
 - 社区：有AI研究室、信息研究室、理论研究室等

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7xpu4m2qj30nx0dedle.jpg)

- 3. `结构等价`（Structural equivalence）：若节点u和节点v与所有其他节点拥有相同的关系，则称节点u和节点v结构等价。

换句话说：对所有的其他节点集 𝑘, 当且仅当（iff/if and only）节点 𝑢 和 𝑘之间的连接，等同于节点 𝑣 和 𝑘之间的连接。如下图的 结构等价的节点4和节点5（6/7也是一组）：

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7xrtu2boj30il0810t3.jpg)

### 2.1 为什么Roles很重要？（why）

因为有用！且应用广泛！

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7xsbr2ukj30eo06ydgo.jpg)

### 2.2 怎么找到Structural Roles？（how）

#### 2.2.1 通过 RolX 方法[1]

简单来说，就是递归抽取节点特征，然后做无监督的聚类。

该方法有以下特点：

- 无监督学习方法
- 无需先验知识
- 为每个节点分配不同Roles混合而成的成员关系
- 复杂度随着网络中的**边的数量**`线性`增长

#### 2.2.2 流程说明：

RolX 方法具体流程图如下：大致两个阶段：

1. 递归特征抽取（Recursive feature extraction）
2. 角色抽取（Role Extraction）

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7xv1gnwoj30u00fjdkg.jpg)

##### 2.2.2.1 递归特征抽取

目的是**将节点转化为特征向量**，该特征向量包含了该节点本身、节点的邻居节点的向量的信息，然后使用它`递归`生成新的特征。

**基础特征**

- （自身和邻居）基础特征特征工程思路总结

- - 对有向图，包括入度、出度、总度数
  - 对加权图：包括加权的各个度特征，即权重聚合
  - 邻居的平均聚类系数
  - egonet的内部边数，指向egonet边数，指出边数，总边数等。

> egonet: 某节点和它的邻居，以及这些节点之间的所有边构成的诱导子图。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7xybwbr6j30bv0603yo.jpg)

##### 2.2.2.2 算法步骤

1. 以基础特征集作为开始

2. 使用当前节点特征集生成新特征

3. - 寻找高度相关的特征对

   - 当两个特征的相关性超过用户定义的阈值时，删除其中一个特征

   - **使用mean和sum两种聚合函数**

   - **剪枝操作**

     随着每次递归迭代，生成特征的数量呈指数增长 ($2^k$)。故需要使用`剪枝`来减少特征数量

   - **重复2**

##### 2.2.2.3 角色抽取

RolX 对特征矩阵进行`非负矩阵分解`，得到最终结果。

- 最小描述长度进行特征筛选（MDL：principle of minimum description length）。
- KL散度评估相似度。

### 2.3 应用举例

老师举了论文合作者网络角色挖掘的例子。

![图片](https://tva1.sinaimg.cn/large/008eGmZEly1gn7y0utwddj30om0awwiy.jpg)

## 3 总结

本节，介绍的RolX思想上虽然比较好理解，但是知和行之间还是有不小的差距。计划通过代码运行，加深理解，同时看能不能在实际业务中运用。论文为资料5，供参考。

## 4 参考文章

- https://blog.csdn.net/lssx0817/article/details/106195822
- 《网络科学引论》郭世泽 陈哲译
- https://snap-stanford.github.io/cs224w-notes/preliminaries/motifs-and-structral-roles_lecture
- Automatic discovery of nodes’ structural roles in networks [Henderson, et al. 2011b]
- http://web.eecs.umich.edu/~dkoutra/papers/12-kdd-recursiverole.pdf

