## 删除子树
https://www.1point3acres.com/bbs/thread-815871-1-1.html

想象一个三层的complete binary tree，7个node，写成题目中所说的array，就应该是0，0，0，1，1，2，2。之前忘了说root node的parent指针指向自己。
index 0是root。

index 1 & 2是0，因为这两个是root的两个children，他们的val是parent的index，which is 0

index 3 & 4是1，因为这两个是root.left的两个children，他们的val是parent的index，which is 1

index 5 & 6是2，因为这两个是root.right的两个children，他们的val是parent的index，which is 2

如果remove掉root.left这个node，那么root.left.left和root.left.right也都要被remove，删掉3个node剩下4个node，

要求输出这4个node得到的array，应该是0，0，1，1

```py
def removeSubtree(tree, target_index):
    """
    nodeToRemove: 记录需要删除的节点
    newIndexMap: old_index --> new_index
    """
    
    # 第一次traverse, 添加所有需要删除的节点
    nodeToRemove = {target_index}
    for i, parent in enumerate(tree):
        if parent in nodeToRemove:
            nodeToRemove.add(i)
    
    # 第二次traverse, map
    # update res
    newIndexMap = dict()
    new_i = 0
    res = []
    for i, parent in enumerate(tree):
        newIndexMap[i] = new_i
        if i not in nodeToRemove:
            new_i += 1
            res.append(newIndexMap[parent])

    return res

def test():

    tree = [0, 0, 0, 1, 1, 2, 2]
    target_index = 1
    print(removeSubtree(tree, target_index))

    tree = [0, 0, 1, 1]
    target_index = 1
    print(removeSubtree(tree, target_index))

test()

"""
[0, 0, 1, 1]
"""
```
