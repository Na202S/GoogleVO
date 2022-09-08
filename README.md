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


## LC 1146. Snapshot Array
https://leetcode.com/problems/snapshot-array/
```py
class SnapshotArray:

    def __init__(self, length: int):
        # array中的元素为list
        # list中的元素为[snap_id, val]pair
        self.snap_id = 0
        self.array = [[[-1, 0]] for _ in range(length)]

        
    def set(self, index: int, val: int) -> None:
        self.array[index].append([self.snap_id, val])

        
    def snap(self) -> int:
        self.snap_id += 1
        return self.snap_id - 1
        

    def get(self, index: int, snap_id: int) -> int:
        # 二分查找 <= snap_id 的最大id
        pairs = self.array[index]
        lo, hi = 0, len(pairs) - 1
        while lo < hi:
            mid = (lo + hi + 1) // 2
            cur_snap_id = pairs[mid][0]
            if cur_snap_id > snap_id:
                hi = mid - 1
            else:
                lo = mid
        return pairs[lo][1]
        
        # bisect.bisect / bisect.bisect_right
        # 在 a 中找到 x 合适的插入点以维持有序，如果 x 已经在 a 里存在，那么插入点会在已存在元素之后（也就是右边）。
        # 即
        # 返回的插入点 i 将数组 a 分成两半
        # 使得左半边为 all(val <= x for val in a[lo : i]) 而右半边为 all(val > x for val in a[i : hi])
        # targetID = bisect.bisect(pairs, [snap_id + 1]) - 1
        # return pairs[targetID][1]


# Your SnapshotArray object will be instantiated and called as such:
# obj = SnapshotArray(length)
# obj.set(index,val)
# param_2 = obj.snap()
# param_3 = obj.get(index,snap_id)
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
        i = randrange(self.N)
        return i if i not in self.mp else self.mp[i]
        


# Your Solution object will be instantiated and called as such:
# obj = Solution(n, blacklist)
# param_1 = obj.pick()
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

