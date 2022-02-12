---
layout: post
title: 2022-02-12leetcode刷题-array相关
tags: [leetcode,面试]
category: leetcode
toc: true
math: true
author: zzhc
---

### 除自身以外数组的乘积

#### 方法一：左右乘积列表
思路

利用索引左侧所有数字的乘积和右侧所有数字的乘积（即前缀与后缀）相乘得到答案。


算法

初始化两个空数组 L 和 R。
对于给定索引 i，L[i] 代表的是 i 左侧所有数字的乘积。
R[i] 代表的是 i 右侧所有数字的乘积。
我们需要用两个循环来填充 L 和 R 数组的值。对于数组 L，L[0] 应该是 1，因为第一个元素的左边没有元素。对于其他元素：L[i] = L[i-1] * nums[i-1]。

同理，对于数组 R，R[length-1] 应为 1。length 指的是输入数组的大小。其他元素：R[i] = R[i+1] * nums[i+1]。

当 R 和 L 数组填充完成，我们只需要在输入数组上迭代，且索引 i 处的值为：L[i] * R[i]。


![enter description here](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_12_前缀.jpeg)

![enter description here](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_12_后缀.jpeg)

```shell
复杂度分析

时间复杂度：O(N)O(N)，其中 NN 指的是数组 nums 的大小。预处理 L 和 R 数组以及最后的遍历计算都是 O(N)O(N) 的时间复杂度。

空间复杂度：O(N)O(N)，其中 NN 指的是数组 nums 的大小。使用了 L 和 R 数组去构造答案，L 和 R 数组的长度为数组 nums 的大小。
```

#### 方法二：空间复杂度 O(1)O(1) 的方法

思路

尽管上面的方法已经能够很好的解决这个问题，但是空间复杂度并不为常数。

由于输出数组不算在空间复杂度内，那么我们可以将 L 或 R 数组用输出数组来计算。先把输出数组当作 L 数组来计算，然后再动态构造 R 数组得到结果。让我们来看看基于这个思想的算法。

算法

初始化 answer 数组，对于给定索引 i，answer[i] 代表的是 i 左侧所有数字的乘积。
构造方式与之前相同，只是我们试图节省空间，先把 answer 作为方法一的 L 数组。
这种方法的唯一变化就是我们没有构造 R 数组。而是用一个遍历来跟踪右边元素的乘积。并更新数组 answer[i]=answer[i]*Ranswer[i]=answer[i]∗R。然后 RR 更新为 R=R*nums[i]R=R∗nums[i]，其中变量 RR 表示的就是索引右侧数字的乘积。




***
<br>
<br>