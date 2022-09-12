## 删叶子
https://leetcode.com/discuss/interview-question/1693416/Google-or-Onsite-or-Recursively-delete-leave-nodes-in-a-multi-tree

### (1) 不断删除**二叉树**叶子节点，直到树为空，返回任意的删除顺序。

类似 LC 366: https://leetcode.com/problems/find-leaves-of-binary-tree/
```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def findLeaves(self, root: Optional[TreeNode]) -> List[List[int]]:
        """
        DFS 计算节点的高度 (叶子节点为1, 叶子节点的parent为2,...,依此类推)
        heightToNodes:dict, key为高度，val为节点list
        """
        heightToNodes = defaultdict(list)
        
        def dfs(node):
            if not node:
                return 0
            left_h = dfs(node.left)
            right_h = dfs(node.right)
            height = max(left_h, right_h) + 1
            heightToNodes[height].append(node.val)
            return height
        
        dfs(root)
        return heightToNodes.values()
```

### (2) 不再是二叉树，需要重新设计节点，返回任意的删除顺序。
- follow-up a: 要求新的叶子节点出现后，尽可能晚地删除新节点
- follow-up b: 要求新的叶子节点出现后，最先删除新节点

两种设计方法
#### 方法1: 使用 list 存放 children
```py
from collections import deque

class TreeNode:
    def __init__(self, val) -> None:
        self.val = val
        self.children = []

"""
在dfs的时候统计node的children数量, 记录在children_cnt中, 同时记录parent
类似于拓扑排序的思想：
- 将叶子(children_cnt为0的节点)存放在queue中
- 从queue中取出节点, "删除"(update该节点parent的children_cnt)
- 如果parent变成了叶子(parent的children_cnt变为了0)
- 按照要求将其加入queue的
  - 左边：最先删
  - 右边：最后删
"""
def removeLeaves(root:TreeNode, removeNewLeavesFirst:bool):
    
    children_cnt, parent = dict(), dict()

    def dfs(node):
        if not node:
            return
        children_cnt[node] = len(node.children)
        for child in node.children:
            parent[child] = node
            dfs(child)
        
    
    dfs(root)
    res = []
    queue = deque([node for node in children_cnt if children_cnt[node] == 0])

    while queue:
        cur_node = queue.popleft()
        res.append(cur_node.val)
        if cur_node in parent:
            cur_parent = parent[cur_node]
            children_cnt[cur_parent] -= 1
            if children_cnt[cur_parent] == 0:
                # 最先删/最后删的区别仅在于此
                if removeNewLeavesFirst:
                    queue.appendleft(cur_parent)
                else:
                    queue.append(cur_parent)
    return res
```

- 测试用例
```py
def buildTree():
    """
         1
     /   |   \
    2    5    3
   / \   | 
  7   4  9
      |
      8
    """

    root = TreeNode(1)
    root.children = [TreeNode(2), TreeNode(5), TreeNode(3)]
    root.children[0].children = [TreeNode(7), TreeNode(4)]
    root.children[1].children = [TreeNode(9)]
    root.children[0].children[1].children = [TreeNode(8)]
    return root

def testRemoveLeaves():
    root = buildTree()
    res_first = removeLeaves(root, True)
    print("remove new leaves first res: ", res_first)
    res_last = removeLeaves(root, False)
    print("remove new leaves last res: ", res_last)
 

testRemoveLeaves()
```

- output
```
remove new leaves first res:  [7, 8, 4, 2, 9, 5, 3, 1]
remove new leaves last res:  [7, 8, 9, 3, 4, 5, 2, 1]
```


#### 方法2: 使用 set 存放 children
```py
from collections import deque
"""
如果真remove，用set存放children以降低复杂度
算法基本一样，除了不必再用children_cnt
"""
class TreeNode:
    def __init__(self, val) -> None:
        self.val = val
        self.children = set()

def removeLeaves(root, removeNewLeavesFirst):

    parent = dict()
    queue = deque()

    def dfs(node):
        if not node:
            return
        if not node.children:
            queue.append(node)
        else:
            for child in node.children:
                parent[child] = node
                dfs(child)

    res = []
    dfs(root)
    while queue:
        cur_node = queue.popleft()
        res.append(cur_node.val)
        if cur_node in parent:
            cur_parent = parent[cur_node]
            # 真remove
            cur_parent.children.remove(cur_node)
            if not cur_parent.children:
                if removeNewLeavesFirst:
                    queue.appendleft(cur_parent)
                else:
                    queue.append(cur_parent)
    return res
```

- 测试用例
```py
def buildTree():
    """
         1
     /   |   \
    2    5    3
   / \   | 
  7   4  9
      |
      8
    """

    root = TreeNode(1)
    level1 = [TreeNode(2), TreeNode(5), TreeNode(3)]
    level2_1 = [TreeNode(7), TreeNode(4)]
    root.children = set(level1)
    level1[0].children = set(level2_1)
    level1[1].children = {TreeNode(9)}
    level2_1[1].children = {TreeNode(8)}
    return root

def testRemoveLeaves():
    root = buildTree()
    res_first = removeLeaves(root, True)
    print("remove new leaves first res: ", res_first)
    root = buildTree()
    res_last = removeLeaves(root, False)
    print("remove new leaves last res: ", res_last)
 

testRemoveLeaves()
```

- output
```
remove new leaves first res:  [3, 9, 5, 8, 4, 7, 2, 1]
remove new leaves last res:  [9, 3, 8, 7, 5, 4, 2, 1]
```
