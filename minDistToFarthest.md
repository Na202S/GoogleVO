## Minimize the distance to the farthest point
https://leetcode.com/discuss/interview-question/algorithms/285144/interview-question-minimize-the-distance-to-the-farthest-point

Assume you're looking to move, and have a set of amenities that you want to have easy access to from your new home. You have found a neighborhood you like, each block of which has zero or more amenities. How would you pick the block to live in such that the farthest distance to any amenity in your list is minimized?

```
Example:
Say your list contains {school, grocery}, and the blocks are as follows:
1: restaurant, grocery
2: movie theater
3: school
4:
5: school
```

The ideal choice would be block 2, such that the distances to the grocery and the nearest school are 1 each. Living on block 1 or 3 would make one of the distances zero, but the other one 2.

```py
from collections import defaultdict
from math import inf

def pickBlock(blocks, amenities_req):
    """
    滑动窗口:
    找到最短的, 包含所有req的amenity的sub array
    取其中点即可
    """

    # window维护当前sub array中各个amenity的数量
    window = defaultdict(int)

    def addBlock(block):
        for new_amenity in block:
            if new_amenity in amenities_req:
                window[new_amenity] += 1
    
    def removeBlock(block):
        for old_amenity in block:
            if old_amenity in amenities_req:
                window[old_amenity] -= 1
                # 为0, 则删除该 amenity
                if window[old_amenity] == 0:
                    del window[old_amenity]
    
    lo = 0
    n = len(blocks)
    minWindowLen = inf
    res = -1
    for hi in range(n):
        # add右边
        addBlock(blocks[hi])
        # 当前window满足要求
        while len(window) == len(amenities_req):
            # 计算当前sub array长度
            curWindowLen = hi - lo + 1
            # update最短长度
            if curWindowLen < minWindowLen:
                minWindowLen = curWindowLen
                # pick中点
                res = (lo + hi) >> 1
            # remove左边
            removeBlock(blocks[lo])
    # 1-index
    return res + 1


def test():
    blocks = [
        ["restaurant", "grocery"],
        ["movie theater"],
        ["school"],
        [],
        ["school"]
    ]
    
    need = {"school", "grocery"}

    print(pickBlock(blocks, need))

test()
"""
2
"""
```
