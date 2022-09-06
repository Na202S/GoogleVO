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

