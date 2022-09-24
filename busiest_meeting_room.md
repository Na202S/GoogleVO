## Busiest meeting room

https://leetcode.com/discuss/interview-question/1566515/GoogleorOnsiteorBusiest-meeting-room

There are K check up room in sequence (indexed 0 - K-1)in a long corridor and you are given the entry time of different patients and duration of check up in order.
The check up room is assigned on the basis of closest check up room available to incoming patient.
Once check up is finished it can be assigned again.

Find the check up rooms which catered to maximum number of patients.

```py
"""
clarify:
k: # rooms
patients: a list of [entry time, duration]
"""
import heapq
def busiestRoom(k, patients):
    # 2 min_heap
    ready = [(0, i) for i in range(k)] # (readyTime, index)
    taken = [] # (endTime, index)
    counter = [0] * k
    for s, duration in patients:
        # 释放所有已经结束的room，从taken转到ready
        while taken and taken[0][0] <= s:
            t, i = heapq.heappop(taken)
            heapq.heappush(ready, (t, i))
        # 有可用的room，则使用，从ready转到taken，计数
        if ready:
            t, i = heapq.heappop(ready)
            heapq.heappush(taken, (s+duration, i))
            counter[i] += 1
        # 没有可用的room，等待最先结束的room，更新endTime，计数
        else:
            t, i = heapq.heappop(taken)
            heapq.heappush(taken, (t+duration, i))
            counter[i] += 1
    return counter.index(max(counter))
             

def test():
    k = 2
    patients = [[1, 3], [2, 4], [4, 10], [4, 5], [6, 7]]
    print(busiestRoom(k, patients))

test() # 1
```

