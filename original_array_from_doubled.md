## LC 2007. Find Original Array From Doubled Array
https://leetcode.com/problems/find-original-array-from-doubled-array/
```py
class Solution:
    def findOriginalArray(self, changed: List[int]) -> List[int]:
        # 排序
        arr = sorted(changed)
        dq = deque()
        res = []
        
        for x in arr:
            # 是2x, 从dq弹出
            if dq and x == dq[0]:
                dq.popleft()
                
            # x加入res, 2x加入dq
            else:
                dq.append(2*x)
                res.append(x)
        
        if dq:
            return []
        return res
```

## Find Original Array From Doubled Array
https://leetcode.com/discuss/interview-question/1419055/Google-Phone-Screen

需要考虑负数的情况，for e.g.,[4,-2,2,-4] 按绝对值排序后 [-2, 2, 4, -4], 原方法会报错。

参考 LC 954: https://leetcode.com/problems/array-of-doubled-pairs/

input : An array will contain integer elements and their doubles in randomized indices. need to write a function to return the original array. ( only the original elements and not doubles, duplicates and **negatives【和 LC 2007 的区别】** can also be there), **if the input is invalid then just throw exception**.

ex:
input: [1, 6, 4, 3, 2, 2]
output: [1, 2, 3]

```py
import collections

def findOriginalArray(arr):
    arr.sort(key=abs)
    cnt = collections.Counter(arr)
    res = []
    for x in arr:
        if cnt[x] == 0:
            continue
        elif cnt[2*x] > 0:
            res.append(x)
            cnt[x] -= 1
            cnt[2*x] -= 1
        else:
            # raise Exception("Not a valid input")
            return "Not a valid input"
    return res

def test():
    print(findOriginalArray([1, 6, 4, 3, 2, 2]))
    print(findOriginalArray([1,3,4,2,6,8]))
    print(findOriginalArray([6,3,0,1]))
    print(findOriginalArray([4,-2,2,-4]))

test()
"""
[1, 2, 3]
[1, 3, 4]
Not a valid input
[-2, 2]
"""
```


