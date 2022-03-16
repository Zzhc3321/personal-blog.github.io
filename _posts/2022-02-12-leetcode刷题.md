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

```python
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


```














***
<br>

<br>

### 236. 二叉树的最近公共祖先

**strong text**


#### 后序遍历



```python 

# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
 
class Solution:
    res = None
 
    def dfs(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        # 可以使用后序遍历来进行解决
        if root is None: return False
        lson = self.dfs(root.left, p, q)
        rson = self.dfs(root.right, p, q)
        if (lson and rson) or ((p.val == root.val or q.val == root.val) and (lson or rson)):
            self.res = root
        return lson or rson or p.val == root.val or q.val == root.val
 
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        self.dfs(root, p, q)
        return self.res
        
```
























***
<br>

<br>

### 100. 相同的树

**strong text**


#### 思路

```python 

class Solution:

    def isSameTree(self, p: TreeNode, q: TreeNode) -> bool:
        if p is None or q is None:
            return p==q
        return self.isSameTree(p.left,q.left) and p.val==q.val and self.isSameTree(p.right,q.right)

```




























***
<br>

<br>

### 111. 二叉树的最小深度

**strong text**


#### 层次遍历


```python 
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def minDepth(self, root: TreeNode) -> int:
        #层次遍历
        if root is None:
            return 0
        st = [root]
        level = 0
        while st:
            level += 1
            n = len(st)
            for i in range(n):
                elm = st.pop(0)
                if elm.left is not None:
                    st.append(elm.left)
                if elm.right is not None:
                    st.append(elm.right)
                if elm.left is None and elm.right is None:
                    return level
            
```
























***
<br>

<br>

### 295. 数据流的中位数

**strong text**


#### 思路

用两个优先队列**queMax**和**queMin**分别记录大于中位数的数和小于等于中位数的数。
- 当累计添加的数的数量为奇数时，**queMin** 中的数的数量比 **queMax** 多一个，此时中位数为 **queMin** 的队头。
- 当累计添加的数的数量为偶数时，两个优先队列中的数的数量相同，此时中位数为它们的队头的平均值。


```python 

class MedianFinder:

    def __init__(self):
        self.queMin = list()
        self.queMax = list()

    def addNum(self, num: int) -> None:
        queMin_ = self.queMin
        queMax_ = self.queMax

        if not queMin_ or num <= -queMin_[0]:
            heapq.heappush(queMin_, -num)
            if len(queMax_) + 1 < len(queMin_):
                heapq.heappush(queMax_, -heapq.heappop(queMin_))
        else:
            heapq.heappush(queMax_, num)
            if len(queMax_) > len(queMin_):
                heapq.heappush(queMin_, -heapq.heappop(queMax_))
        
    def findMedian(self) -> float:
        queMin_ = self.queMin
        queMax_ = self.queMax

        if len(queMin_) > len(queMax_):
            return -queMin_[0]
        return (-queMin_[0] + queMax_[0]) / 2

```

















***
<br>

<br>

### 23. 合并K个排序链表

**将所有链表合并到一个升序链表中，返回合并后的链表。**


#### 思路

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if len(lists)==0 or (len(lists)==1 and lists[0]==None):
            return None
        res = ListNode()
        n = len(lists)
        r = res
        i_s = [i for i in range(n)]
        for i in range(n):
            if lists[i]==None:
                i_s.remove(i)
                
        while i_s:
            p = i_s[0]
            for i in i_s:
                if lists[i].val<lists[p].val:
                    p = i
            r.next = lists[p]
            r = r.next
            lists[p] = lists[p].next
            if lists[p]==None:
                i_s.remove(p)
        r.next = None
        return res.next
```
































***
<br>

<br>

### 373. 查找和最小的K对数字

**给定两个以 升序排列 的整数数组 nums1 和 nums2 , 以及一个整数 k 。定义一对值 (u,v)，其中第一个元素来自 nums1，第二个元素来自 nums2 。请找到和最小的 k 个数对 (u1,v1),  (u2,v2)  ...  (uk,vk) 。**


#### 堆

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        m, n = len(nums1), len(nums2)

        all_elm = [(i+j,i,j) for i in nums1 for j in nums2]

        heapq.heapify(all_elm)

        res = heapq.nsmallest(k,all_elm)

        ans = []

        for elm in res:
            (_,i,j) = elm
            ans.append([i,j])

        return ans
```































***
<br>

<br>

### 451. 根据字符出现频率排序

**strong text**


#### 堆


```python
class Solution:
    def frequencySort(self, s: str) -> str:
        dic = {}
        for elm in s:
            if elm in dic:
                dic[elm] += 1
            else:
                dic[elm] = 1

        ans = [(-v,k) for (k,v) in dic.items()]

        heapq.heapify(ans)

        n = len(ans)

        res = ""

        for i in range(n):
            elm = heapq.heappop(ans)
            res += elm[1]*(-elm[0])


        return res
```


































***
<br>

<br>

### 871. 最低加油次数

 - **每个 station[i] 代表一个加油站，它位于出发位置东面 station[i][0]英里处，并且有 station[i][1] 升汽油。**
 - **目的地位于出发位置东面 target 英里处。**
 - **假设汽车油箱的容量是无限的，其中最初有 startFuel 升燃料。它每行驶 1英里就会用掉 1 升汽油。**

#### 思路
1. 动规
```python 
class Solution(object):
    def minRefuelStops(self, target, startFuel, stations):
        dp = [startFuel] + [0] * len(stations)
        for i, (location, capacity) in enumerate(stations):
            for t in xrange(i, -1, -1):
                if dp[t] >= location:
                    dp[t+1] = max(dp[t+1], dp[t] + capacity)
        for i, d in enumerate(dp):
            if d >= target: return i
        return -1
```

2. 优先队列

```python
class Solution(object):
    def minRefuelStops(self, target, tank, stations):
        pq = []  # A maxheap is simulated using negative values
        stations.append((target, float('inf')))

        ans = prev = 0
        for location, capacity in stations:
            tank -= location - prev
            while pq and tank < 0:  # must refuel in past
                tank += -heapq.heappop(pq)
                ans += 1
            if tank < 0: return -1
            heapq.heappush(pq, -capacity)
            prev = location

        return ans
```


























***
<br>

<br>

### 230. 二叉搜索树中第K小的元素

**给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。**


#### 右子树的最左下角
```python 
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def deleteNode(self, root: Optional[TreeNode], key: int) -> Optional[TreeNode]:
        if root is None:
            return None
        if root.val==key:
            if root.left is None:
                return root.right
            elif root.right is None:
                return root.left

        def dr_node(pre, root):
            p = root
            if p.right is None:
                if pre.left==p:
                    pre.left = p.left
                else:
                    pre.right = p.left

            elif p.left is None:
                if pre.left==p:
                    pre.left = p.right
                else:pre.right = p.right
            else:
                pre = p
                p=p.right
                while p.left:
                    pre = p
                    p=p.left
                root.val = p.val
                dr_node(pre, p)


        def inorder(root, key, pre=None):
            if root is None:
                return
            inorder(root.left, key, root)

            if root.val>key:
                return
            elif root.val==key:
                dr_node(pre, root)
            
            inorder(root.right, key, root)

        inorder(root, key)
        return root
```




























***
<br>

<br>

### 450. 删除二叉搜索树中的节点

**给定一个二叉搜索树的根节点 root ，和一个整数 k ，请你设计一个算法查找其中第 k 个最小元素（从 1 开始计数）。**


#### 中序遍历

```python 
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:

        def in_order(root, res_list):
            if root is None:
                return
            
            in_order(root.left, res_list)
            res_list.append(root.val)
            in_order(root.right, res_list)

        sort_list = []
        in_order(root, sort_list)

        return sort_list[k-1]
```













***
<br>

<br>

### 49. 字母异位词分组

**给你一个字符串数组，请你将字母异位词组合在一起。可以按任意顺序返回结果列表。字母异位词 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。**


#### 散列表（排序、统计）


```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        dicts = {}

        for st in strs:
            key = sorted(st)
            str_key = ''.join(key)
            if str_key in list(dicts.keys()):
                dicts[str_key].append(st)
            else:
                dicts[str_key] = [st]
        return list(dicts.values())
```












***
<br>

<br>

***
Graph

***


### 785. 判断二分图

**二分图 定义：如果能将一个图的节点集合分割成两个独立的子集A和B，并使图中的每一条边的两个节点一个来自 A 集合，一个来自 B 集合，就将这个图称为 二分图 。如果图是二分图，返回 true ；否则，返回 false 。**


#### BFS

```python
class Solution:
    def isBipartite(self, graph: List[List[int]]) -> bool:
        seta = []
        setb = []
        n = len(graph)
        visited = [False for i in range(n)]
  
        for i in range(n):
            if visited[i]:
                continue
            else:
                seta.append(i)
                q = [i]
                while q:
                    elm = q.pop()
                    if visited[elm]:
                        continue
                    visited[elm] = True
                    if elm in seta:
                        for i in graph[elm]:
                            q.append(i)
                            if i in seta:
                                return False
                            setb.append(i)
                    else:
                        for i in graph[elm]:
                            q.append(i)
                            if i in setb:
                                print(i)
                                return False
                            seta.append(i)
        return True
```







***
<br>

<br>

### 215. 数组中的第K个最大元素

**strong text**


#### 排个序返回








***
<br>

<br>

### 969. 煎饼排序

**例如，arr = [3,2,1,4] ，选择 k = 3 进行一次煎饼翻转，反转子数组 [3,2,1] ，得到 arr = [1,2,3,4] 。以数组形式返回能使 arr 有序的煎饼翻转操作所对应的 k 值序列。任何将数组排序且翻转次数在 10xarr length范围内的有效答案都将被判断为正确。** 


#### 每次定位最大的反转到最终位置

```python
class Solution:
    def pancakeSort(self, arr: List[int]) -> List[int]:
        res = []
        n = len(arr)
        def fanzhuan(a):
            return [a[-i-1] for i in range(len(a))]
        
        for i in range(n):
            loc = arr.index(max(arr[:n-i]))
            if loc!=n-i-1:
                if loc!=0:
                    arr[:loc+1] = fanzhuan(arr[:loc+1])
                    res.append(loc+1)
                arr[:n-i] = fanzhuan(arr[:n-i])
                res.append(n-i)
        return res
```




























***
<br>

<br>

***
Search

***

### 200. 岛屿数量

**给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。此外，你可以假设该网格的四条边均被水包围。**


#### 思路：BFS

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        m,n = len(grid),len(grid[0])
        visited = [[False for j in range(n)] for i in range(m)]

        num = 0

        for i in range(m):
            for j in range(n):
                if visited[i][j]:
                    continue
                if grid[i][j] == '0':
                    continue

                q = [(i,j)]
                visited[i][j]=True
                while q:
                    elm = q.pop()
                    loci,locj = elm[0],elm[1]
                    if loci>0:
                        if grid[loci-1][locj]=='1' and visited[loci-1][locj] is not True:
                            q.append((loci-1,locj))
                            visited[loci-1][locj] = True
                    if loci<m-1:
                        if grid[loci+1][locj]=='1' and visited[loci+1][locj] is not True:
                            q.append((loci+1,locj))
                            visited[loci+1][locj] = True
                    if locj>0:
                        if grid[loci][locj-1]=='1' and visited[loci][locj-1] is not True:
                            q.append((loci,locj-1))
                            visited[loci][locj-1] = True
                    if locj<n-1:
                        if grid[loci][locj+1]=='1' and visited[loci][locj+1] is not True:
                            q.append((loci,locj+1))
                            visited[loci][locj+1] = True
                    
                num+=1
        return num
```








***
<br>

<br>

### 407. 接雨水 II

**给你一个 m x n 的矩阵，其中的值均为非负整数，代表二维高度图每个单元的高度，请计算图中形状最多能接多少体积的雨水。**


#### 思路

1. 设方块 (i,j)(i,j) 的最终的高度为 h[i][j]h[i][j]，那么 h[i][j] = max(heightMap[i][j], min(h[i-1][j], h[i+1][j], h[i][j-1], h[i][j +1]))h[i][j]=max(heightMap[i][j],min(h[i−1][j],h[i+1][j],h[i][j−1],h[i][j+1]))。

2. 优先从矮的的方块开始找，一个格子周围有四个方块，但最终的盛水量，取决于最矮一个。
   
3. 计算完当前方块盛水量，要更新当前点的高度，h[i][j] = max(heightMap[i][j], min(h[i-1][j], h[i+1][j], h[i][j-1], h[i][j +1]))h[i][j]=max(heightMap[i][j],min(h[i−1][j],h[i+1][j],h[i][j−1],h[i][j+1]))。































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

**假设你正在爬楼梯。需要 n 阶你才能到达楼顶。每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？**


#### 思路：动态规划

```python 
class Solution:
    def climbStairs(self, n: int) -> int:
        if n<=1:
            return n

        dp1 = 1
        dp2 = 2
        for i in range(3,n+1):
            dp1,dp2 = dp2,dp1+dp2
        
        return dp2
```


































***
<br>

<br>

### 300. 最长上升子序列

**给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。子序列 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。**


#### 思路：动态规划{dp[i] = max(dp[i],dp[j]+1)}

```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:

        n = len(nums)
        dp = [1 for i in range(n)]

        for i in range(1,n):
            for j in range(i):
                if nums[i]>nums[j]:
                    dp[i] = max(dp[i],dp[j]+1)

        return max(dp)
```




























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