## Path effort less than threshold

https://leetcode.com/discuss/interview-question/1568857/Google-or-Phone-or-Is-path-possible-less-than-threshold

Given a gird of MxN cells, with Integer cell values, we can define a path effort of a path from source (0,0) to the destination (M-1, N-1) as the max of the absolute differences of the adjacent cell values. While traversing, you can move up, down, left and right — 4 moves from the current cell in the grid. For a given Threshold value, find if it is possible to have a path in such a matrix with the effort not greater than the threshold value, from source to destination.


Example:
If we have a 3x3 matrix as follows -
```
[[1, 2, 3],
[4, 5, 6],
[7, 8, 9]]
Threshold = 4
Result: YES
```
As, 1->2->3->6->9 or 1->2->5->6->9 or ...

```py
from collections import deque
"""
简单BFS
"""
def findPath(grid, threshold):
    m, n = len(grid), len(grid[0])
    queue = deque([(0, 0)])
    seen = set([(0, 0)])
    while queue:
        i, j = queue.popleft()
        if (i, j) == (m-1, n-1):
            return True
        for x, y in [(i-1, j), (i+1, j), (i, j-1), (i, j+1)]:
            # 符合访问要求的点
            if 0 <= x < m and 0 <= y < n and (x, y) not in seen:
                seen.add((x, y))
                diff = abs(grid[x][y] - grid[i][j])
                # 符合effort要求的点
                if diff <= threshold:
                    queue.append((x, y)) 
    return False
```  
