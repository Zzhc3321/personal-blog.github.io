---
layout: post
title: 2022-06-03-Transfer_Learning 
tags: [深度学习,tl]
category: 深度学习
toc: true
math: true
author: 知乎：王晋东不在家
---


《小王爱迁移》系列

一些缺乏的知识点

 - maximum mean discrepancy
 - 核函数（核技巧）



## 迁移成分分析(TCA)

&emsp;&emsp;迁移成分分析（TCA，transfer component analysis）通过降维来达到数据维度减少，而且能达到迁移学习目的。

&emsp;&emsp;用通俗的语言来说，跟PCA很像：PCA是一个大矩阵进去，一个小矩阵出来，TCA是两个大矩阵进去，两个小矩阵出来。从学术角度讲，TCA针对domain adaptation问题中，源域和目标域处于不同数据分布时，将两个领域的数据一起映射到一个高维的再生核希尔伯特空间。在此空间中，最小化源和目标的数据距离，同时最大程度地保留它们各自的内部属性。

&emsp;&emsp;TCA本质是完成迁移学习的要求。迁移学习的要求是让源域和目标域距离尽可能小。

### 假设
源域和目标域的边缘分布是不一样的。TCA假设存在一个特征映射$\phi$，使得映射后数据的分布近似相同。

### 具体
TCA利用的距离叫做最大均值差异（MMD，maximum mean discrepancy）。这个距离的公式如下：
![enter description here](http://img.zzhc321.xyz/blog/1654222864451.png)


### 求解

![enter description here](http://img.zzhc321.xyz/blog/1654242720190.png)


### 总结
输入是两个特征矩阵，我们首先计算L和H矩阵，然后选择一些常用的核函数进行映射（比如线性核、高斯核）计算K，接着求![\[公式\]](http://img.zzhc321.xyz/blog/1654242760400.png)的前m个特征值。然后，得到的就是源域和目标域的降维后的数据，我们就可以在上面用传统机器学习方法了。


<br>

<br>

***

## 联合分布适配(JDA)

&emsp;&emsp;简单概括就是用有标注的源域数据来标定完全无标注的目标域。JDA方法就是要适配源域和目标域的联合概率。

### 假设

 - 源域和目标域边缘分布不同
 - 源域和目标域条件分布不同

&emsp;&emsp;JDA方法的目标就是，寻找一个变换![\[公式\]](http://img.zzhc321.xyz/blog/1654244928638.png)，使得经过变换后的![\[公式\]](http://img.zzhc321.xyz/blog/1654244941968.png) 和 ![\[公式\]](http://img.zzhc321.xyz/blog/1654244954250.png)的距离能够尽可能地接近，同时，![\[公式\]](http://img.zzhc321.xyz/blog/1654244959949.png)和![\[公式\]](http://img.zzhc321.xyz/blog/1654244965290.png)的距离也要小。

### 具体

 1. 边缘分布适配：其实这个操作就是迁移成分分析（TCA）。我们仍然使用MMD距离来最小化源域和目标域的最大均值差异。

 2. 条件分布适配：我们依然没有/y_t。采用的方法是，用(X_s,Y_s)来训练一个简单的分类器（比如knn、逻辑斯特回归），到/X_t上直接进行预测。总能够得到一些伪标签[公式]的吧。我们根据伪标签来计算，这个问题就可解了