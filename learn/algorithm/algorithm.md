### 动态规划

例 4-9. 四种方法计算梵文旋律：（一）迭代；（二）自底向上的动态规划；（三）自上而
下的动态规划；（四）内置默记法。
def virahanka1(n):
    if n == 0:
        return [""]
    elif n == 1:
        return ["S"]
    else:
        s = ["S" + prosody for prosody in virahanka1(n-1)]
        l = ["L" + prosody for prosody in virahanka1(n-2)]
    return s + l

def virahanka2(n):
    lookup = [[""], ["S"]]
    for i in range(n-1):
        s = ["S" + prosody for prosody in lookup[i+1]]
        l = ["L" + prosody for prosody in lookup[i]]
        lookup.append(s + l)
    return lookup[n]

def virahanka3(n, lookup={0:[""], 1:["S"]}):
    if n not in lookup:
        s = ["S" + prosody for prosody in virahanka3(n-1)]
        l = ["L" + prosody for prosody in virahanka3(n-2)]
        lookup[n] = s + l
    return lookup[n]

迭代：形式简介，大量的无用计算
自底向上：空间换时间，保留中间结果，避免重复计算
自上而下：空间换时间，而且有些中间结果是不需要用到的，这里可以避免这种计算

* BST中序遍历的结果，就是一个排好序的list


### Graph

* BFS与DFS在程序结构上完全一致，区别仅在于使用stack还是fifo队列。
* 函数调用基于stack，因此递归天生就是DFS

```python
def traverse(graph, start, dfs):
    visited, queue = set(), [start]
    while queue:
        node = queue.pop() if dfs else queue.popleft()  # dfs-stack or bfs-fifo
        if node not in visited:                         # for circled graph
            visited.add(node)
            for nextNode in graph[node]:
                if nextNode not in visited:             # for multi-parent node
                    queue.append(nextNode)

```

### Recursive to Iterative

* 用栈来保存递归函数参数

```
"""
Given a binary tree and a sum, determine if the tree has a root-to-leaf
path such that adding up all the values along the path equals the given sum.

For example:
Given the below binary tree and sum = 22,
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1
return true, as there exist a root-to-leaf path 5->4->11->2 which sum is 22.
"""
def has_path_sum_recursive(root, sum):
    if not root:
        return False
    if not root.left and not root.right and root.val = sum:
        return True
    left = has_path_sum_recursive(root.left, sum-root.val)
    right = has_path_sum_recursive(root.right, sum-root.val)
    return left or right

# DFS with stack, What should be push in stack!
def has_path_sum_iterative(root, sum):
    if not root:
        return False
    stack = [(root, root.val)]
    while stack:
        node, val = stack.pop()
        if not node.left and not node.right:
            if val == sum:
                return True
        if node.left:
            stack.append((node.left, val+node.left.val))
        if node.right:
            stack.append((node.right, val+node.right.val))
    return False

```
### BST
前序、中序、后序的遍历，理解的关键点在于：
* 前、中、后指的是，父节点的顺序
* 对于子节点，一定是先左后右


算法 | 遍历顺序
---------|----------
 前序 | 父节点——左子节点——右子节点 
 中序 | 左子节点——父节点——右子节点 
 后序 | 左子节点——右子节点——父节点 

```
def pre_traversal(root):
    if not root:
        return [root.val] + pre_traversal(root.left) + pre_traversal(root.right)

def pre_traversal_r2l(root):
    if not root:
        return [root.val] + pre_traversal(root.right) + pre_traversal(root.left)


def mid_traversal(root):
    if not root:
        return mid_traversal(root.left) + [root.val] + mid_traversal(root.right)

```

### 如何找到递归结构？
* 增加条件

F(n)与F(n-1)并没有直接的关系，不妨给F(n)的定义加上条件限制。
如下例子，当限定f(n)为以n为根节点的树个数时，产生的递归关系：
$$F(n) = F(0) * F(n-1) + F(1) * F(n-2) + F(2) * F(n-3) + ... + F(n-2) * F(1) + F(n-1) * F(0)$$
```python
"""
Given n, how many structurally unique BST's
(binary search trees) that store values 1...n?

For example,
Given n = 3, there are a total of 5 unique BST's.

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
"""


"""
Taking 1~n as root respectively:
1 as root: # of trees = F(0) * F(n-1)  // F(0) == 1
2 as root: # of trees = F(1) * F(n-2)
3 as root: # of trees = F(2) * F(n-3)
...
n-1 as root: # of trees = F(n-2) * F(1)
n as root:   # of trees = F(n-1) * F(0)

So, the formulation is:
F(n) = F(0) * F(n-1) + F(1) * F(n-2) + F(2) * F(n-3) + ... + F(n-2) * F(1) + F(n-1) * F(0)
"""

def num_trees(n):
    """
    :type n: int
    :rtype: int
    """
    dp = [0] * (n+1)
    dp[0] = 1
    dp[1] = 1
    for i in range(2, n+1):
        for j in range(i+1):
            dp[i] += dp[i-j] * dp[j-1]
    return dp[-1]
```


### 其他技巧
* 考虑双栈，或双队列

### 经典问题
* 动态规划——最长公共子串
