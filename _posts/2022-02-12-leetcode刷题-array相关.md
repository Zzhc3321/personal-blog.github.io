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

### 946. 验证栈序列
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

**给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。。**


#### 思路：堆

```python 
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        # 注意 Python 默认的优先队列是小根堆
        q = [(-nums[i], i) for i in range(k)]
        
        heapq.heapify(q)

        ans = [-q[0][0]]
        for i in range(k, n):
            heapq.heappush(q, (-nums[i], i))
            while q[0][1] <= i - k:
                heapq.heappop(q)
            ans.append(-q[0][0])
        
        return ans
```


#### 思路：单调队列
运用双端队列，实现单调队列。
![双端队列](https://gitee.com/zzhc3321/personal-blog-drawing-bed/raw/master/personal-blog/2022_2_19_1645279627496.png)

```python 
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        q = collections.deque()
        for i in range(k):
            while q and nums[i] >= nums[q[-1]]:
                q.pop()
            q.append(i)

        ans = [nums[q[0]]]
        for i in range(k, n):
            while q and nums[i] >= nums[q[-1]]:
                q.pop()
            q.append(i)
            while q[0] <= i - k:
                q.popleft()
            ans.append(nums[q[0]])
        
        return ans

```

















***
<br>

<br>

### 206. 反转链表

**strong text**


#### 头插法






















***
<br>

<br>

### 287. 寻找重复数

**一个包含 n + 1 个整数的数组 nums ，其数字都在 [1, n] 范围内（包括 1 和 n），假设 nums 只有 一个重复的整数 ，返回 这个重复的数 。只用常量级 O(1) 的额外空间。**


#### 快慢指针（Floyd 判圈算法）

```python
class Solution:
    def findDuplicate(self, nums: List[int]) -> int:
        fast = nums[nums[0]]
        low = nums[0]

        while fast!=low:
            fast = nums[nums[fast]]
            low = nums[low]

        low = 0

        while nums[fast]!=nums[low]:
            fast = nums[fast]
            low = nums[low]
        
        return nums[low]
```























***
<br>

<br>

### 19. 删除链表的倒数第N个节点

**strong text**


#### 思路

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        if head.next is None and n==1:
            return None

        p = ListNode(head.val,head.next)
        for i in range(n):
            p = p.next
        
        m = ListNode(0,head)
        cur = m

        if p:
            cur = cur.next
            while p.next is not None:
                cur = cur.next
                p = p.next
        
        cur.next = cur.next.next

        return m.next
```
















***
<br>

<br>

### 105. 从前序与中序遍历序列构造二叉树

**strong text**


#### 递归

# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        def p_i_to_tree(root, preor, inor):
            if preor:
                root.val = preor[0]
            else: 
                root = None
                return
            
            n = len(inor)

            i = 0
            while i<n and root.val!=inor[i]:
                i+=1

            if i > 0:
                root.left = TreeNode(preor[1])
                p_i_to_tree(root.left, preor[1:i+1],inor[0:i])
            if i < n-1:
                root.right = TreeNode(preor[i+1])
                p_i_to_tree(root.right, preor[i+1:],inor[i+1:])
            
        root = TreeNode()
        p_i_to_tree(root,preorder,inorder)

        return root



















***
<br>

<br>

### 236. 二叉树的最近公共祖先

**strong text**


#### 思路




























***
<br>

<br>

### 100. 相同的树

**strong text**


#### 思路






























***
<br>

<br>

### 111. 二叉树的最小深度

**strong text**


#### 思路





























***
<br>

<br>

### 295. 数据流的中位数

**strong text**


#### 思路





















***
<br>

<br>

### 23. 合并K个排序链表

**strong text**


#### 思路



































***
<br>

<br>

### 373. 查找和最小的K对数字

**strong text**


#### 思路

































***
<br>

<br>

### 451. 根据字符出现频率排序

**strong text**


#### 思路





































***
<br>

<br>

### 871. 最低加油次数

**strong text**


#### 思路





























***
<br>

<br>

### 230. 二叉搜索树中第K小的元素

**strong text**


#### 思路




























***
<br>

<br>

### 450. 删除二叉搜索树中的节点

**strong text**


#### 思路






























***
<br>

<br>

### 700.二叉搜索树中的搜索

**strong text**


#### 思路
































***
<br>

<br>

### 701. 二叉搜索树中的插入操作

**strong text**


#### 思路




























***
<br>

<br>

***
Graph

***
### 938. 二叉搜索树的范围和

**strong text**


#### 思路






***
<br>

<br>

### 785. 判断二分图

**strong text**


#### 思路
































***
<br>

<br>

***
Sort

***

### 33. 搜索旋转排序数组

**strong text**


#### 思路




























***
<br>

<br>

### 81. 搜索旋转排序数组 II

**strong text**


#### 思路








***
<br>

<br>

### 215. 数组中的第K个最大元素

**strong text**


#### 思路
































***
<br>

<br>

### 969. 煎饼排序

**strong text**


#### 思路




























***
<br>

<br>

***
Search

***

### 200. 岛屿数量

**strong text**


#### 思路








***
<br>

<br>

### 407. 接雨水 II

**strong text**


#### 思路
































***
<br>

<br>

***
DynamicProgramming

***

### 72. 编辑距离

**strong text**


#### 思路




























***
<br>

<br>

### 32. 最长有效括号

**strong text**


#### 思路









***
<br>

<br>

### 70. 爬楼梯

**strong text**


#### 思路
































***
<br>

<br>

### 300. 最长上升子序列

**strong text**


#### 思路




























***
<br>

<br>

### 322. 零钱兑换

**strong text**


#### 思路








***
<br>

<br>

***
GraphAlgorithm

***

### 743. 网络延迟时间

**strong text**


#### 思路
































***
<br>

<br>

### 207. 课程表

**strong text**


#### 思路




























***
<br>

<br>

### 210. 课程表 II

**strong text**


#### 思路









***
<br>

<br>

### 630. 课程表 III

**strong text**


#### 思路
































***
<br>

<br>

### 329. 矩阵中的最长递增路径

**strong text**


#### 思路









***
<br>

<br>

### 题

**strong text**


#### 思路