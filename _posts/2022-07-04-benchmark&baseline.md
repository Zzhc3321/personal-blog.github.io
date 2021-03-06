---
layout: post
title: 2022-07-04-benchmark&baseline
tags: [指标,模型]
category: 深度学习
toc: true
math: true
author: zzhc
---


## benchmark
&emsp;&emsp;一个算法之所以被称为benchmark，是因为它的性能已经被广泛研究，人们对它性能的表现形式、测量方法都非常熟悉，因此可以作为标准方法来衡量其他方法的好坏。这里需要区别state-of-the-art（SOTA），能够称为SOTA的算法表明其性能在当前属于最佳性能。如果一个新算法以SOTA作为benchmark，这当然是最好的了，但如果比不过SOTA，能比benchmark要好，且方法有一定创新，也是可以发表的。

## baseline
&emsp;&emsp;一个算法被称为baseline，基本上表示比这个算法性能还差的基本上不能接受的，除非方法上有革命性的创新点，而且还有巨大的改进空间和超越benchmark的潜力，只是因为是发展初期而性能有限。所以baseline有一个自带的含义就是“性能起点”。这里还需要指出其另一个应用语境，就是在算法优化过程中，一般version1.0是作为baseline的，即这是你的算法能达到的一个基本性能，在算法继续优化和调参数的过程中，你的目标是比这个性能更好，因此需要在这个base line的基础上往上跳。




## 总结

 - benchmark一般是和同行中比较牛的算法比较，比牛算法还好，那你可以考虑发好一点的会议/期刊；
 - baseline一般是自己算法优化和调参过程中自己和自己比较，目标是越来越好，当性能超过benchmark时，可以发表了，当性能甚至超过SOTA时，考虑投顶会顶刊啦。