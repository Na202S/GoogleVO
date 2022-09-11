# Google VO
## 餐厅
https://leetcode.com/discuss/interview-experience/1461079/google-swe-l3l4-dublin-ireland-reject

Build a data structure to perform three operations (Restaurant is full initially):
1) waitList (string customer_name, int table_size):
Add customer with given name and table size they want to book into the waitlist
2) leave (string customer_name):
Customer wants to leave the waitlist so remove them.
3) serve (int table_size):
This means restaurant now has a free table of size equal to table_size. Find the best customer to serve from waitlist
Best Customer: Customer whose required size is less than or equal to the table_size. If multiple customers are matching use first come first serve.
For e.g. if waitlist has customers with these table requirements => [2, 3, 4, 5, 5, 7] and restaurant is serving table_size = 6 then best customer is index 3 (0-based indexing).

```py
from sortedcontainers import SortedDict
from collections import deque
import bisect

class Restaurant():

    def __init__(self) -> None:
        # key: tableSize, val: deque([customer, ...])
        self.tableToCustomers = SortedDict()
        # key: customer, val: tableSize
        self.waitingList = {}


    def waitList(self, customerName, tableSize):
        self.waitingList[customerName] = tableSize
        queue = self.tableToCustomers.setdefault(tableSize, deque())
        queue.append(customerName)


    def leave(self, customerName):
        tableSize = self.waitingList[customerName]
        queue = self.tableToCustomers[tableSize]
        queue.remove(customerName)         # remove from deque
        del self.waitingList[customerName] # remove from wl

        # remove tableSize from sortedDict if no customer waiting for it
        if not self.tableToCustomers[tableSize]:
            del self.tableToCustomers[tableSize]


    def serve(self, tableSize):
        if not self.waitingList:
            print("Waiting list is empty.")
            return
        tableSizes = self.tableToCustomers.keys()
        i = bisect.bisect_left(tableSizes, tableSize - 1) # binary search
        tableSizeRequest = tableSizes[i]
        if (tableSizeRequest > tableSize):
            print("No any customer waiting for Table of size: ", tableSize, " or below.")
            return
        queue = self.tableToCustomers[tableSizes[i]]
        customerName = queue[0]
        self.leave(customerName)
        return customerName


def test():
    # test case example
    res = Restaurant()
    res.waitList('Alice', 5)
    res.waitList('Bob', 7)
    res.waitList('Cam', 3)
    res.waitList('Den', 5)
    res.waitList('Eva', 4)
    res.waitList('Fu', 2)
    print("tableToCustomers:\n", res.tableToCustomers)
    print("waitingList:\n",res.waitingList)

    customerToServe = res.serve(1)
    print("serve customer: ", customerToServe)

    # test leave funtion
    res.leave("Eva")
    res.leave("Den")
    print("tableToCustomers:\n", res.tableToCustomers)
    print("waitingList:\n",res.waitingList)

test()
```

## LC 1146. Snapshot Array
https://leetcode.com/problems/snapshot-array/
```py
class SnapshotArray:
    """
    搞一个长度为length的数组array，每一个index上存放的是list
    每一次set(index, val)，将[snap_id, val]加入array[index]
    """
    def __init__(self, length: int):
        self.snap_id = 0
        self.array = [[[-1, 0]] for _ in range(length)]

        
    def set(self, index: int, val: int) -> None:
        self.array[index].append([self.snap_id, val])

        
    def snap(self) -> int:
        self.snap_id += 1
        return self.snap_id - 1
        

    def get(self, index: int, snap_id: int) -> int:
        # 二分查找 <= snap_id 的最大id
        # pairs = self.array[index]
        # lo, hi = 0, len(pairs) - 1
        # while lo < hi:
        #     mid = (lo + hi + 1) // 2
        #     cur_snap_id = pairs[mid][0]
        #     if cur_snap_id > snap_id:
        #         hi = mid - 1
        #     else:
        #         lo = mid
        # return pairs[lo][1]
        
        # bisect.bisect / bisect.bisect_right
        # 在 a 中找到 x 合适的插入点以维持有序，如果 x 已经在 a 里存在，那么插入点会在已存在元素之后(也就是右边)
        # bisect.bisect_left: 如果 x 已经在 a 里存在，那么插入点会在已存在元素之后(也就是左边)
        pairs = self.array[index]
        targetID = bisect.bisect(pairs, [snap_id + 1]) - 1
        return pairs[targetID][1]
```

## LC 388. Longest Absolute File Path
https://leetcode.com/problems/longest-absolute-file-path/
```py
class Solution:
    def lengthLongestPath(self, input: str) -> int:
        res = 0
        lengthMap = {-1: 0}
        for line in input.split('\n'):
            depth = line.count('\t')
            # 如果是文件，更新当前最长路径
            if '.' in line:
                res = max(res, lengthMap[depth - 1] + len(line))
            # 如果是目录，更新 map: depth --> length (包含‘\’)
            else:
                lengthMap[depth] = lengthMap[depth - 1] + len(line) - depth
        return res
```

## lyrics
https://www.1point3acres.com/bbs/thread-816037-1-1.html
给定一个string of lyrics, 一个target word. 

输出是否这个长lyrics 包含target word的每一个字母，其中lyrics每一个词包含target word的一个字母。 

例子 "hel[l], h[o]w are yo[u]", target："lou",  return true.

```py


```

## LC 710. Random Pick with Blacklist
https://leetcode.com/problems/random-pick-with-blacklist/
```py
class Solution:
    """
    random pick x in [0, N - 1]
    若 x 在 blacklist 中，则将其 map 到 [N, n - 1]中不在 blacklist 中的数字
    否则，可直接返回
    """
    def __init__(self, n: int, blacklist: List[int]):
        self.N = n - len(blacklist)
        blacklistSet = set(blacklist)
        keys = []
        self.mp = {}
        # 遍历 blacklist，将需要 map 的 key 添加到 keys 中
        for x in blacklist:
            if x < self.N:
                keys.append(x)
        # map
        i = 0
        # 遍历 [N, n]，不在 blacklist 中的数字可以作为 val
        for j in range(self.N, n):
            if j not in blacklistSet:
                self.mp[keys[i]] = j
                i += 1
        

    def pick(self) -> int:
        # 生成 [0, N - 1] 的随机整数
        i = randrange(self.N)
        return i if i not in self.mp else self.mp[i]
        


# Your Solution object will be instantiated and called as such:
# obj = Solution(n, blacklist)
# param_1 = obj.pick()
```

## LC 329. Longest Increasing Path in a Matrix
https://leetcode.com/problems/longest-increasing-path-in-a-matrix/
```py
class Solution:
    def longestIncreasingPath(self, matrix: List[List[int]]) -> int:
        """
        dp[i][j]: 从 (i, j) 出发的 Longest Increasing Path 长度
        dfs计算dp
        遍历 (i, j)，取dp[i][j]最大值
        """
        m, n = len(matrix), len(matrix[0])
        dp = [[0] * n for _ in range(m)]
        def compute_path(i, j):
            max_path = 1
            # cache
            if dp[i][j]:
                return dp[i][j]
            # try 向 4 个方向走，取其中的最大值
            for x, y in (i - 1, j), (i + 1, j), (i, j - 1), (i, j + 1):
                # 可行
                if 0 <= x < m and 0 <= y < n and matrix[x][y] > matrix[i][j]:
                    cur_path = 1 + compute_path(x, y)
                    max_path = max(max_path, cur_path)
            # 从 (i, j) 出发的 Longest Increasing Path 长度
            dp[i][j] = max_path
            return max_path
        
        # 遍历 (i, j)，取dp[i][j]最大值
        res = 1
        for i in range(m):
            for j in range(n):
                res = max(res, compute_path(i, j))
        return res
```

## LC 56. Merge Intervals
https://leetcode.com/problems/merge-intervals/
```py
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        """
        按 start 排序
        不能合并则直接加入
        能合并则更新 end 时间
        """
        res = []
        intervals.sort(key=lambda x:x[0])
        for interval in intervals:
            # res 为空，或者没有 overlap (新start > 旧end)
            if not res or interval[0] > res[-1][1]:
                res.append(interval)
            # 更新 end，取 max(旧end, 新end)
            else:
                res[-1][1] = max(res[-1][1], interval[1])
        return res
```

## LC 1631. Path With Minimum Effort
https://leetcode.com/problems/path-with-minimum-effort/

### Dijkstra
```py
# import heapq -- for heap
# import math  -- for inf
"""
Time complexity - O(E + VlogV)
function Dijkstra(Graph, source):
  for each vertex v in Graph:
    distance[v] = infinity
    
 distance[source] = 0
 G = the set of all nodes of the Graph
 
 while G is non-empty:
     Q = node in G with the least dist[ ]
     mark Q visited
     for each neighbor N of Q:
         alt_dist = distance[Q] + dist_between(Q, N)
         if alt-dist < distance[N]
             distance[N] := alt_dist
             
 return distance[ ]
"""

class Solution:
    def minimumEffortPath(self, heights: List[List[int]]) -> int:
        """
        Dijkstra: O(m*n*log(m*n))
        dists[row][col] = 当前的 (0,0) --> (row,col) 最短路径距离
        minHeap存放 (dists[row][col], row, col)
        初始时，all dists = inf, except (0, 0) with dist 0
        
        Dijkstra, 每次取出距离 source 最近的 node，update所有与node相连的node的dist
        """
        m, n = len(heights), len(heights[0])
        dists = [[math.inf] * n for _ in range(m)]
        dists[0][0] = 0
        minHeap = [(0, 0, 0)]
        
        while minHeap:
            dist, i, j = heapq.heappop(minHeap)
            if (i, j) == (m-1, n-1):
                return dist
            for x, y in (i-1, j), (i+1, j), (i,j-1), (i,j+1):
                if 0 <= x < m and 0 <= y < n:
                    newDist = max(dist, abs(heights[x][y] - heights[i][j]))
                    if newDist < dists[x][y]:
                        dists[x][y] = newDist
                        heapq.heappush(minHeap, (dists[x][y], x, y))
```

### BFS + binary search

```py
from collections import deque

class Solution:
    def minimumEffortPath(self, heights: List[List[int]]) -> int:
        """
        BFS + binary search
        O(m*n*log(maxHeight)), maxHeight = 10^6
        二分查找最小的 effort
        BFS检查给定 maxEffrot 时，是否存在符合条件的路径
        """
        m, n = len(heights), len(heights[0])
    
        def canReach(maxEffort):
            seen = {(0, 0)}
            queue = deque([(0, 0)])
            while queue:
                i, j = queue.popleft()
                if (i, j) == (m - 1, n - 1):
                    return True
                for x, y in (i, j-1), (i, j+1), (i-1, j), (i+1, j):
                    if 0 <= x < m and 0 <= y < n and abs(heights[x][y]-heights[i][j]) <= maxEffort and (x, y) not in seen:
                        seen.add((x, y))
                        queue.append((x, y))
            return False
        
        lo, hi = 0, 10**6
        while lo < hi:
            mid = (lo + hi) >> 1  # avoid TLE
            if canReach(mid):
                hi = mid
            else:
                lo = mid + 1
        return lo
```

## LC 542. 01 Matrix
https://leetcode.com/problems/01-matrix/
```py
class Solution:
    def updateMatrix(self, mat: List[List[int]]) -> List[List[int]]:
        """
        BFS: O(m*n)
        将所有‘0’加入queue，所有‘1’设为-1 (表示尚未计算)
        只要queue不为空：访问四方，计算尚未计算的格子距离，加入queue
        """
        m, n = len(mat), len(mat[0])
        queue = deque()
        for i in range(m):
            for j in range(n):
                if mat[i][j] == 0:
                    queue.append((i, j))
                else:
                    mat[i][j] = -1
        while queue:
            i, j = queue.popleft()
            for x, y in (i - 1, j), (i + 1, j), (i, j - 1), (i, j + 1):
                if 0 <= x < m and 0 <= y < n and mat[x][y] == -1:
                    mat[x][y] = mat[i][j] + 1
                    queue.append((x, y))
        return mat
```

## LC 965. Univalued Binary Tree
https://leetcode.com/problems/univalued-binary-tree/
```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isUnivalTree(self, root: Optional[TreeNode]) -> bool:
        """
        recursion: O(N)
        """
        # 递归出口
        if not root:
            return True
        # check左右子节点
        if (root.left and root.left.val != root.val) or (root.right and root.right.val != root.val):
            return False
        # check左右子树
        return self.isUnivalTree(root.left) and self.isUnivalTree(root.right)
```

## 微波炉
https://www.1point3acres.com/bbs/thread-826368-1-1.html
```py
def lazyMicrowave(time):
    time_split = time.split(":")
    minutes, seconds = time_split[0], time_split[1]
    method1 = minutes + seconds
    # only one method available
    if int(seconds) > (99 - 60):
        return method1
    method2 = str(int(minutes)-1) + str(int(seconds)+60)
    return method1 if computeTime(method1) <= computeTime(method2) else method2

def computeTime(method):
    cost = 4
    for i in range(1, len(method) - 1):
        if method[i] != method[i - 1]:
            cost += 2
    return cost


time = "12:06"
print(lazyMicrowave(time))
```

