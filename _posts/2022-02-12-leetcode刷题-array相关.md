---
layout: post
title: 2022-02-12leetcode刷题
tags: [leetcode,面试]
category: leetcode
toc: true
math: true
author: zzhc
---

### 238.除自身以外数组的乘积
**给你一个整数数组 nums，返回 数组 answer ，其中 answer[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积 。**


#### 思路：左右乘积列表

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


***
<br>
<br>

### 224.基本计算器
**给你一个字符串表达式 s ，请你实现一个基本计算器来计算并返回它的值。（只有加减）**

#### 思路：括号展开 + 栈

使用一个取值为 {−1,+1} 的整数 代表「当前」的符号。根据括号表达式的性质，它的取值：

- 与字符串中当前位置的运算符有关；
- 如果当前位置处于一系列括号之内，则也与这些括号前面的运算符有关：每当遇到一个以号开头的括号，则意味着此后的符号都要被「翻转」。

```python
def calculate(s: str) -> int:
        ops = [1]
        sign = 1
        ret = 0
        n = len(s)
        i = 0
        while i < n:
            if s[i] == ' ':
                i += 1
            elif s[i] == '+':
                sign = ops[-1]
                i += 1
            elif s[i] == '-':
                sign = -ops[-1]
                i += 1
            elif s[i] == '(':
                ops.append(sign)
                i += 1
            elif s[i] == ')':
                ops.pop()
                i += 1
            else:
                num = 0
                while i < n and s[i].isdigit():
                    num = num * 10 + int(s[i])
                    i += 1
                ret += num * sign
        return ret
```


***
<br>
<br>

### 456.132模式

**&emsp;&emsp;给你一个整数数组 nums ，数组中共有 n 个整数。132 模式的子序列 由三个整数 nums[i]、nums[j] 和 nums[k] 组成，并同时满足：i < j < k 和 nums[i] < nums[k] < nums[j] 。如果 nums 中存在 132 模式的子序列 ，返回 true ；否则，返回 false 。**

#### 思路：单调栈（解决陡峭问题）

![单调栈](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_18_单调栈.png)

```python
class Solution:
    def find132pattern(self, nums: List[int]) -> bool:
        n = len(nums)
        st = [nums[n-1]]
        m = float ('-inf')
        for i in range(n-2,-1,-1):
            if nums[i]<m:
                return True
            while len(st)>0 and nums[i]>st[-1]: #保持递减
                m = st.pop()
            st.append(nums[i])
        return False
```

***
<br>
<br>

### 402.移掉K位数字
**给你一个以字符串表示的非负整数 num 和一个整数 k ，移除这个数中的 k 位数字，使得剩下的数字最小。请你以字符串形式返回这个最小的数字。**

#### 思路：单调栈
![单调栈](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_19_1645273260627.png)

我的答案：
```python
class Solution:
    def removeKdigits(self, num: str, k: int) -> str:
        st = []
        n = 0
        done = False
        if len(num)<=k:
            return '0'

        for s in num:
            if len(st)==0:
                st.append(s)
                continue
            while int(s)<int(st[-1]) and not done:
                st.pop()
                n+=1
                if n==k:
                    done = True
                if len(st)==0:
                    break
            st.append(s)

        while n<k:
            n+=1
            st.pop()

        while st[0]=='0':
            del st[0]
            if len(st)==0:
                return '0'

        return ''.join(st)

```

<br>
官方答案：
```python
class Solution:
    def removeKdigits(self, num: str, k: int) -> str:
        numStack = []
        
        # 构建单调递增的数字串
        for digit in num:
            while k and numStack and numStack[-1] > digit:
                numStack.pop()
                k -= 1
        
            numStack.append(digit)
        
        # 如果 K > 0，删除末尾的 K 个字符
        finalStack = numStack[:-k] if k else numStack
        
        # 抹去前导零
        return "".join(finalStack).lstrip('0') or "0"
```

***
<br>

<br>

###946. 验证栈序列
**给定 pushed 和 popped 两个序列，每个序列中的值都不重复，只有当它们可能是在最初空栈上进行的推入 push 和弹出 pop 操作序列的结果时，返回 true；否则，返回 false 。**


#### 思路：
```python 
class Solution(object):
    def validateStackSequences(self, pushed, popped):
        j = 0
        stack = []
        for x in pushed:
            stack.append(x)
            while stack and j < len(popped) and stack[-1] == popped[j]:
                stack.pop()
                j += 1

        return j == len(popped)
```



***
<br>

<br>

### 225. 用队列实现栈

**请你仅使用两个队列实现一个后入先出（LIFO）的栈，并支持普通栈的全部四种操作（push、top、pop 和 empty）。实现 MyStack 类**


#### 思路

```python 
class MyStack:

    def __init__(self):
        self.queue1 = []
        self.queue2 = []

    def push(self, x: int) -> None:
        self.queue1.insert(0,x)

    def pop(self) -> int:
        while len(self.queue1)>1:
            self.queue2.insert(0,self.queue1.pop())
        res = self.queue1.pop()
        while len(self.queue2):
            self.queue1.insert(0,self.queue2.pop())
        return res

    def top(self) -> int:
        while len(self.queue1)>1:
            self.queue2.insert(0,self.queue1.pop())
        res = self.queue1[0]
        self.queue2.insert(0,self.queue1.pop())
        while len(self.queue2):
            self.queue1.insert(0,self.queue2.pop())
        return res


    def empty(self) -> bool:
        return len(self.queue1)==0


# Your MyStack object will be instantiated and called as such:
# obj = MyStack()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.top()
# param_4 = obj.empty()
```





***
<br>

<br>

### 239. 滑动窗口最大值

**strong text**


#### 思路






















***
<br>

<br>

### 621. 任务调度器

**strong text**


#### 思路

















***
<br>

<br>

### 622. 设计循环队列

**strong text**


#### 思路





















***
<br>

<br>

### 206. 反转链表

**strong text**


#### 思路






















***
<br>

<br>

### 287. 寻找重复数

**strong text**


#### 思路





















***
<br>

<br>

### 19. 删除链表的倒数第N个节点

**strong text**


#### 思路





















***
<br>

<br>

### 题

**strong text**


#### 思路




















***
<br>

<br>

### 题

**strong text**


#### 思路




























***
<br>

<br>

### 题

**strong text**


#### 思路






























***
<br>

<br>

### 题

**strong text**


#### 思路





























***
<br>

<br>

### 题

**strong text**


#### 思路





















***
<br>

<br>

### 题

**strong text**


#### 思路



































***
<br>

<br>

### 题

**strong text**


#### 思路

































***
<br>

<br>

### 题

**strong text**


#### 思路





































***
<br>

<br>

### 题

**strong text**


#### 思路





























***
<br>

<br>

### 题

**strong text**


#### 思路




























***
<br>

<br>

### 题

**strong text**


#### 思路






























***
<br>

<br>

### 题

**strong text**


#### 思路
































***
<br>

<br>

### 题

**strong text**


#### 思路




























***
<br>

<br>

### 题

**strong text**


#### 思路