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
 
 ### b) LC 
