## 最少block几个cell
https://leetcode.com/discuss/interview-question/1564556/Google-or-Onsite-or-Minimum-number-of-cells-to-be-blocked

You are given a 2D matrix which has some cells blocked. Find the minimum number of cells to be blocked so that you can't reach the end i.e (m-1,n-1) from 0,0. 

You can go only towards right and bottom.

Follow Up: You can go in all 4 directions.

```py
import copy
from collections import deque

"""
clarify: 
grid[m][n], 1 represents blocked, otherwise 0
should not block (0, 0) or (m-1, n-1)
"""
def minCells(grid):
    """
    最多2个: (0, 1) & (1, 0)
    1个: try each (i, j)
    0个: the path has been blocked
    """
    
    m, n = len(grid), len(grid[0])
    def hasPath(grid_input):
        if grid_input[0][0]:
            return False
        queue = deque([(0, 0)])
        seen = set([(0, 0)])
        while queue:
            i, j = queue.popleft()
            if (i, j) == (m-1, n-1):
                return True
            for x, y in [(i-1, j), (i+1, j), (i, j-1), (i, j+1)]:
                if 0 <= x < m and 0 <= y < n and grid_input[x][y] == 0:
                    if (x, y) not in seen:
                        seen.add((x, y))
                        queue.append((x, y))
        return False

    grid_copy = copy.deepcopy(grid)
    if not hasPath(grid_copy):
        return 0
    
    for i in range(m):
        for j in range(n):
            if (i, j) == (0, 0) or (i, j) == (m - 1, n - 1):
                continue
            grid_copy = copy.deepcopy(grid)
            grid_copy[i][j] = 1
            if not hasPath(grid_copy):
                return 1
    
    return 2

def test():
    grid = [[0, 0], 
            [0, 0]]
    print(minCells(grid))

    grid = [[0, 0, 1], 
            [0, 1, 0]]
    print(minCells(grid))

    grid = [[0, 0, 1], 
            [0, 0, 0]]
    print(minCells(grid))

test()
"""
2
0
1
"""
```
