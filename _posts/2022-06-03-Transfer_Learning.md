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

## 迁移成分分析(TCA)方法简介

&emsp;&emsp;迁移成分分析（TCA，transfer component analysis）通过降维来达到数据维度减少，而且能达到迁移学习目的。

&emsp;&emsp;用通俗的语言来说，跟PCA很像：PCA是一个大矩阵进去，一个小矩阵出来，TCA呢，是两个大矩阵进去，两个小矩阵出来。从学术角度讲，TCA针对domain adaptation问题中，源域和目标域处于不同数据分布时，将两个领域的数据一起映射到一个高维的再生核希尔伯特空间。在此空间中，最小化源和目标的数据距离，同时最大程度地保留它们各自的内部属性。

&emsp;&emsp;TCA本质是什么呢？完成迁移学习的要求。迁移学习的要求是什么呢？让源域和目标域距离尽可能小。

### 假设


### 具体


### 求解


### 总结
