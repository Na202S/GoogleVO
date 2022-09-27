## Find Bad Commits
https://leetcode.com/discuss/interview-question/1831538/Google-or-Phone-Interview-or-SDE-1-or-Find-bad-commit

https://www.1point3acres.com/bbs/thread-816299-1-1.html

You are working on a project and you noticed that there has been a performance decrease between two releases. 

You have a function:
```boolean worseCommit(int commit1, int commit2)```
that runs performance tests and returns true if commit2 is worse than commit1 and false otherwise.

Find all of the bad commits that have decreased the performance between releases. Assume no improvement in performance.

```
Commit Id: 1, 2, 3, 4, 5, 6, 7, 8, 9
Performance: 10, 10, 10, 8, 8, 8, 5, 5, 5
Output: 4, 7
```

```py
def findBadCommits(n, performance):

    def worseCommit(i, j):
        return performance[j] < performance[i]

    res = []
    # find the 1st bad commits in the given range [lo, hi]
    def binarySearch(lo, hi):
        if lo == hi:
            return
        if lo == hi - 1:
            if worseCommit(lo, hi):
                res.append(hi + 1) # 1-indexed
            return
        
        mid = (lo + hi) >> 1
        # mid 比 lo 差， 说明 1st bad commits 在前半部分
        if worseCommit(lo, mid):
            binarySearch(lo, mid)
        
        binarySearch(mid, hi)

    binarySearch(0, n - 1)
    return res

def test():
    performance = [10, 10, 10, 8, 8, 8, 5, 5, 5]
    print(findBadCommits(9, performance))

test()
```
