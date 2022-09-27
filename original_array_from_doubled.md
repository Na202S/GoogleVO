## LC 2007. Find Original Array From Doubled Array
https://leetcode.com/problems/find-original-array-from-doubled-array/
```py
class Solution:
    def findOriginalArray(self, changed: List[int]) -> List[int]:
        """
        Greedy
        先统计counter，从小到大排列 changed
        删除一个 x
        - 如果存在 2x，则
        -- x属于原数组，删除一个 2x
        - 不存在 2x
        -- invalid input
        """
        counter = Counter(changed)
        res = []
        
        for x in sorted(changed):
            if counter[x]:
                counter[x] -= 1
                if counter[2*x]:
                    res.append(x)
                    counter[2*x] -= 1
                else:
                    return []
        return res
```

## Find Original Array From Doubled Array
https://leetcode.com/discuss/interview-question/1419055/Google-Phone-Screen

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
        if cnt[x]:
            cnt[x] -= 1
            if cnt[2*x]:
                cnt[2*x] -= 1
                res.append(x)
            else:
                # raise Exception("Not a valid input")
                return "Not a valid input"
    return res

def test():
    print(findOriginalArray([1, 6, 4, 3, 2, 2]))
    print(findOriginalArray([1,3,4,2,6,8]))
    print(findOriginalArray([6,3,0,1]))
    print(findOriginalArray([4,-2,2,-4]))
    print(findOriginalArray([0]))


test()
"""
[1, 2, 3]
[1, 3, 4]
Not a valid input
[-2, 2]
Not a valid input
"""
```


