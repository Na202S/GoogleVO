## Find Path in Tree
https://leetcode.com/discuss/interview-experience/1461079/google-swe-l3l4-dublin-ireland-reject

Given a binary tree, find the path directions to go from node A to node B. 

For e.g. for the following binary tree path between 5 to 7 is `["up", "up", "right", "right", "left"]` 

(Note: we can only use "up", "right", "left" directions)

![image](https://user-images.githubusercontent.com/81414394/189750198-de32ff95-20c6-4a9e-b9bf-e875018fe1c2.png)

```py
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None


def findPathInTree(root, nodeA, nodeB):
    
    def findNodeToRootPath(cur_node, target_node, path):
        if cur_node == target_node:
            return True
        if cur_node.left and findNodeToRootPath(cur_node.left, target_node, path):
            path.append("left")
        elif cur_node.right and findNodeToRootPath(cur_node.right, target_node, path):
            path.append("right")
        return path
    
    pathA, pathB = [], []
    findNodeToRootPath(root, nodeA, pathA)
    findNodeToRootPath(root, nodeB, pathB)
    print(pathA, pathB)
    while pathA and pathB and pathA[-1] == pathB[-1]:
        pathA.pop()
        pathB.pop()
    return ["up" for _ in range(len(pathA))] + pathB[::-1]


def test():
    nodes = [TreeNode(i+1) for i in range(7)]
    nodes[0].left, nodes[0].right = nodes[1], nodes[2]
    nodes[1].left, nodes[1].right = nodes[3], nodes[4]
    nodes[2].right = nodes[5]
    nodes[5].left = nodes[6]

    print(findPathInTree(nodes[0], nodes[4], nodes[6]))

test()
"""
['right', 'left'] ['left', 'right', 'right']
['up', 'up', 'right', 'right', 'left']
"""
```



## LC 2096. Step-By-Step Directions From a Binary Tree Node to Another
https://leetcode.com/problems/step-by-step-directions-from-a-binary-tree-node-to-another/


```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def getDirections(self, root: Optional[TreeNode], startValue: int, destValue: int) -> str:
        """
        找到 root 到 start 的 path A， 到 dest 的 path B
        删掉 path A 和 path B 重合的前缀
        将 path A 全部改为 U，与 path B 剩余部分联合即可
        """
        # 由于递归算法，这里的path实际上是start到root & dest到root的路径
        def find(node, val, path):
            if node.val == val:
                return True
            if node.left and find(node.left, val, path):
                path += 'L'
            elif node.right and find(node.right, val, path):
                path += 'R'
            return path
        
        rootToStart, rootToDest = [], []
        find(root, startValue, rootToStart)
        find(root, destValue, rootToDest)
        
        # 因此，common prefix在结尾
        while rootToStart and rootToDest and rootToStart[-1] == rootToDest[-1]:
            rootToStart.pop()
            rootToDest.pop()
        
        # 注意反转path
        return ''.join('U'*len(rootToStart)) + ''.join(reversed(rootToDest))
```
