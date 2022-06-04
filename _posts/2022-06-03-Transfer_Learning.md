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

 2. 条件分布适配：我们依然没有/y_t。采用的方法是，用(X_s,Y_s)来训练一个简单的分类器（比如knn、逻辑斯特回归），到/X_t上直接进行预测。我们根据伪标签来计算。


### 学习策略

把两个距离结合起来，得到了一个总的优化目标：
![enter description here](http://img.zzhc321.xyz/blog/1654245231211.png)


![enter description here](http://img.zzhc321.xyz/blog/1654245244465.png)是正则项


### 总结
&emsp;&emsp;JDA方法比较巧妙，同时适配两个分布，然后非常精巧地规到了一个优化目标里。用弱分类器迭代，最后达到了很好的效果。和TCA的主要区别有两点：

 - 1）TCA是无监督的（边缘分布适配不需要label），JDA需要源域有label；
 - 2）TCA不需要迭代，JDA需要迭代。



<br>

<br>

***

## 深度神经网络的可迁移性

![enter description here](http://img.zzhc321.xyz/blog/1654247275145.png)

&emsp;&emsp;随着可迁移层数的增加，模型性能下降。但是，前3层仍然还是可以迁移的！同时，与随机初始化所有权重比较，迁移学习的精度是很高的!

### 结论

 - 神经网络的前3层基本都是general feature，进行迁移的效果会比较好；
 - 深度迁移网络中加入fine-tune，效果会提升比较大，可能会比原网络效果还好；
 - Fine-tune可以比较好地克服数据之间的差异性；
 - 深度迁移网络要比随机初始化权重效果好；
 - 网络层数的迁移可以加速网络的学习和优化。

<br>
<br>

***


## 深度迁移网络

### DaNN
&emsp;&emsp;（Domain Adaptive Neural Network）的神经网络。DaNN的结构异常简单，它仅由两层神经元组成：特征层和分类器层。


### DDC
&emsp;&emsp;DDC针对预训练的AlexNet（8层）网络，在第7层（也就是feature层，softmax的上一层）加入了MMD距离来减小source和target之间的差异。这个方法简称为DDC。

![enter description here](http://img.zzhc321.xyz/blog/1654309924634.png)


### DAN
DAN相比DDC加了2点改进：

 - 一是多适配了几层特征
 - 二是采用了之前Arthur Gretton提出的多核MMD替换掉原有的单核MMD


&emsp;&emsp;原来的MMD是要把source和target用一个相同的映射映射在一个再生核希尔伯特空间（RKHS）中，然后求映射后两部分数据的均值差异，就当作是两部分数据的差异。最重要的一个概念是核，在MMD中这个核固定的，我们在实现的时候可以选择是高斯核还是线性核。这样的缺点是明显的：我怎么知道哪个核一定好？


&emsp;&emsp;MK-MMD就是为了解决这个问题。它提出用多个核去构造这个总的核，对不同kernel进行加权求和，对于两个概率分布，它们的MK-MMD距离就是
![enter description here](http://img.zzhc321.xyz/blog/1654310077820.png)
![enter description here](http://img.zzhc321.xyz/blog/1654310091483.png)

&emsp;&emsp;DDC和DAN作为深度迁移学习的代表性方法，充分利用了深度网络的可迁移特性，然后又把统计学习中的MK-MMD距离引入，取得了很好的效果。







<br>
<br>

***


## 测地线流式核方法（GFK）


&emsp;&emsp;GFK作为子空间变换方面最为经典的迁移学习方法



### 背景

&emsp;&emsp;GFK方法提出的目的也是为了解决迁移学习中的unsupervised domain adaptation问题：源域数据（source）和目标域数据（target）类别一样，但是特征服从不同的分布。源域数据全部有标注，目标域数据全部无标注。

&emsp;&emsp;已有的方法大多都集中在特征变换上。就是说，通过一个特征映射，把source和target变换到一个公共空间上，在这个空间里，它们的距离是最小的（相似度最高）。这样就能用传统的机器学习方法进行分类了。

&emsp;&emsp;SGF把source和target分别看成高维空间（Grassmann流形）中的两个点，在这两个点的测地线距离上取d个中间点，然后依次连接起来。这样，由source和target就构成了一条测地线的路径。我们只需要找到合适的每一步的变换，就能从source变换到target了。


### 简介


GFK（Geodesic flowkernel）方法首先解决SGF的问题：

 - 如何确定source和target路径上中间点的个数。它通过提出一种kernel方法，利用路径上的所有点的积分，把这个问题解决了。
 - 当有多个source的时候，我们如何决定使用哪个source跟target进行迁移？GFK通过提出Rank of Domain度量，度量出跟target最近的source，来解决这个问题。

**流形学习：**它的基本假设是，现有的数据是从一个高维空间中采样出来的，所以，它具有高维空间中的低维流形结构。

**格拉斯曼流形Grassmann manifold：**有一个n维的向量空间W，W中任意取k维子空间，就构成了Grassmann流形。


**测地线：**两点之间，测地线最短。在流形学习中，我们遇到测量距离的时候，更多的时候用的就是这个测地线。


### 方法

 1. 选择最优的子空间维度：subspace disagreement measure（SDM），翻译过来就是子空间不一致度量。这个度量可以反映出两个domain在多少维的子空间下能够保持最大一致性。

 2. 构建测地线流
    
 3. 测地线流kernel

 3. Rank of Domain：用一个KL散度

<br>
<br>

***

## 从经验中学习迁移

### 背景

Learning To Transfer -> 类似learn to learn

### 方法

 1. 从已有的迁移学习方法和结果中学习迁移的经验

&emsp;&emsp;学习出一个特征的变换矩阵W来。这个W就是这些迁移学习知识的中心。我们直接学习所有迁移对最优的那个W，使得平均误差最小的那个W就是我们要求的。(MMD)


 2. 再把这些学习到的经验应用到新来的数据

&emsp;&emsp;W只是对旧的那些经验学习到的，对新的数据可能效果不好，所以需要对其进行更新。新的W应该是能在新的数据上表现效果最好的那个W。一个适用于增量学习的迁移学习框架，就诞生了。


### 总结

&emsp;&emsp;提出了一个新颖的研究问题：类似于增量学习，如何最大限度地利用已有的迁移学习经验，使得其对新问题的泛化能力很好。











<br>
<br>

***

## 负迁移

&emsp;&emsp;如果两个领域之间不存在，或者基本不相似，可以说出现了负迁移（negative transfer）

>负迁移指的是，在源域上学习到的知识，对于目标域上的学习产生负面作用。