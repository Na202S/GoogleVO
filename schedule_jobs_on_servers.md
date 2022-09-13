## Schedule Jobs on Servers
https://leetcode.com/discuss/interview-question/1285431/Google-Interview-Questions

a) There are n jobs, you have to schedule the jobs on a machine. Given the job start time and duration. Schedule the jobs is best optimal way.

b) In continuation of the above question, there are k machines. Schedule the jobs in the most optimal way.

### a) LC 1834. Single-Threaded CPU
https://leetcode.com/problems/single-threaded-cpu/
```py
class Solution:
    def getOrder(self, tasks: List[List[int]]) -> List[int]:
        """
        task按start排序，记录原index
        记录endTime：上一个task结束的时间
        每一次，将所有start <= endTime的task加入最小堆
        每次取堆中duration最短的task
        如果堆为空，则CPU空转，更新endTime
        """
        tasks = [(t[0], t[1], i) for i, t in enumerate(tasks)]
        tasks.sort()
        endTime = tasks[0][0]
        res = []
        i, n = 0, len(tasks)
        minHeap = []
        while len(res) < n:
            while i < n and tasks[i][0] <= endTime:
                heapq.heappush(minHeap, (tasks[i][1], tasks[i][2]))
                i += 1
            if minHeap:
                duration, index = heapq.heappop(minHeap)
                endTime += duration
                res.append(index)
            # CPU空转
            else:
                endTime = tasks[i][0]
        return res
```   
 
 ### b) LC 1882. Process Tasks Using Servers
https://leetcode.com/problems/process-tasks-using-servers/

```py
class Solution:
    def assignTasks(self, servers: List[int], tasks: List[int]) -> List[int]:
        """
        freeHeap: (weight, index, startTime)
        usedHeap: (endTime, weight, index)
        """
        
        freeHeap = [(w, i, 0) for i, w in enumerate(servers)]
        heapify(freeHeap)
        usedHeap = []
        res = []
        for t, duration in enumerate(tasks):
            # 无可用server
            # 不断移除已结束使用的server
            while not freeHeap or (usedHeap and usedHeap[0][0] <= t):
                startTime, w, i = heappop(usedHeap)
                heappush(freeHeap, (w, i, startTime))
            # 按要求取用server
            w, i, startTime = heappop(freeHeap)
            res.append(i)
            # 将server加入usedHeap
            endTime = max(startTime, t) + duration
            heappush(usedHeap, (endTime, w, i))
        return res
```

- 本题：
```py
import heapq
def assignTasks(k, tasks):
    freeHeap = [(0, i) for i in range(k)]
    heapq.heapify(freeHeap)
    usedHeap = []
    res = []
    for t, duration in tasks:
        while not freeHeap or (usedHeap and usedHeap[0][0] <= t):
            startTime, index = heappop(usedHeap)
            heappush(freeHeap, (startTime, index))
        
        startTime, index = heappop(freeHeap)
        endTime = max(startTime, t) + duration
        heappush(usedHeap, (endTime, index))
        res.append(index)
    return res
``` 
