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
        queue.remove(customerName)         # remove from deque - O(n)
        del self.waitingList[customerName] # remove from wl

        # remove tableSize from sortedDict if no customer waiting for it
        if not self.tableToCustomers[tableSize]:
            del self.tableToCustomers[tableSize]


    def serve(self, tableSize):
        if not self.waitingList:
            print("Waiting list is empty.")
            return
        tableSizes = self.tableToCustomers.keys()
        # keys是unique的，所以
        # 若tableSize在keys中，bisect返回tableSize的后一位，再减1得到tableSize的位置
        # 若tableSize不在keys中，bisect返回tableSize应该插入的位置，再减1得到最接近tableSize且 < tableSize的size位置
        i = bisect.bisect_right(tableSizes, tableSize) - 1 # binary search
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

    customerToServe = res.serve(6)
    print("serve customer: ", customerToServe)

    # test leave funtion
    res.leave("Eva")
    res.leave("Den")
    print("tableToCustomers:\n", res.tableToCustomers)
    print("waitingList:\n",res.waitingList)

test()
```
