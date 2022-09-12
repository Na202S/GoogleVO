## 查找prefix (二分)
https://www.1point3acres.com/bbs/thread-815871-1-1.html

给一个Alphabetically sorted String array和一个prefix，求这个array里面一共有多少个以这个prefix开头的String。
做两次binary search找到第一个和最后一个符合要求的index然后相减。
类似 LC 34: https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/

```py
"""
做两次binary search找到第一个和最后一个符合要求的index然后相减
类似 LC 34
"""

def searchPrefix(words, prefix):
    if not words:
        return 0, []
    
    n = len(words)
    m = len(prefix)
    # 找第一个 >= prefix 的word
    def searchFirst():
        lo, hi = 0, n
        while lo < hi:
            mid = (lo + hi) >> 1
            if words[mid] >= prefix:
                hi = mid
            else:
                lo = mid + 1
        return lo

    left = searchFirst()
    if left == n or words[left][:m] != prefix:
        return 0, []
    
    # 找最后一个含有 prefix 的word
    def searchLast():
        lo, hi = left, n - 1     # start from left instead of 0
        while lo < hi:
            mid = (lo + hi + 1) >> 1
            if words[mid][:m] == prefix:
                lo = mid
            else:
                hi = mid - 1
        return lo

    right = searchLast()
    return right - left + 1, words[left:right+1]

def test():
    array = ["b", "bc", "bca", "bcb", "bcc", "m"]
    print("strings: ", array)
    print("with prefix b: ", searchPrefix(array, "b"))
    print("with prefix bc: ", searchPrefix(array, "bc"))
    print("with prefix bcc: ", searchPrefix(array, "bcc"))
    print("with prefix a: ", searchPrefix(array, "a"))
    print("with prefix z: ", searchPrefix(array, "z"))

    array = []
    print("strings: ", array)
    print("with prefix a: ", searchPrefix(array, "a"))


test()
"""
strings:  ['b', 'bc', 'bca', 'bcb', 'bcc', 'm']
with prefix b:  (5, ['b', 'bc', 'bca', 'bcb', 'bcc'])
with prefix bc:  (4, ['bc', 'bca', 'bcb', 'bcc'])
with prefix bcc:  (1, ['bcc'])
with prefix a:  (0, [])
with prefix z:  (0, [])
strings:  []
with prefix a:  (0, [])
"""
```



## Trie 查找 prefix
Follow up，如果有非常多次Query with prefix操作该怎么办? 

Trie

类似 LC 208: https://leetcode.com/problems/implement-trie-prefix-tree/

LC 1804: https://leetcode.com/problems/implement-trie-ii-prefix-tree/

```py
class TrieNode:
    def __init__(self):
        self.children = dict()
        self.cnt_start_with = 0
        self.isEnd = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
            node.cnt_start_with += 1
        node.isEnd = True
    

    def countWordsStartingWith(self, prefix):
        node = self.root
        for c in prefix:
            if c not in node.children:
                return 0, []
            node = node.children[c]
        cnt = node.cnt_start_with

        words = []
        def dfs(cur_node, prev):
            if not cur_node:
                return
            if cur_node.isEnd:
                words.append(prev)
            for c in cur_node.children:
                dfs(cur_node.children[c], prev + c)
        
        dfs(node, prefix)
        return cnt, words
             
    
def test():
    trie = Trie()
    array = ["b", "bc", "bca", "bcb", "bcc", "m"]
    for word in array:
        trie.insert(word)
    print("strings: ", array)
    print("with prefix b: ", trie.countWordsStartingWith("b"))
    print("with prefix bc: ",trie.countWordsStartingWith("bc"))
    print("with prefix bcc: ",trie.countWordsStartingWith("bcc"))
    print("with prefix a: ", trie.countWordsStartingWith("a"))
    print("with prefix z: ", trie.countWordsStartingWith("z"))

    array = []
    emptyTrie = Trie()
    for word in array:
        trie.insert(word)
    print("strings: ", array)
    print("with prefix a: ",  trie.countWordsStartingWith("a"))

test()
"""
strings:  ['b', 'bc', 'bca', 'bcb', 'bcc', 'm']
with prefix b:  (5, ['b', 'bc', 'bca', 'bcab', 'bcabc'])
with prefix bc:  (4, ['bc', 'bca', 'bcab', 'bcabc'])
with prefix bcc:  (1, ['bcc'])
with prefix a:  (0, [])
with prefix z:  (0, [])
strings:  []
with prefix a:  (0, [])
"""
```


