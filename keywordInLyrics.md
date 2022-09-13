## Lyrics and Keyword
https://leetcode.com/discuss/interview-question/1496278/Google-Phone-Interview-Problem-Lyrics-and-Keyword

https://leetcode.com/discuss/interview-question/1930312/Google-Lyrics-and-Keyword-Interview-Problem

Write a function `canMatch` that takes a string lyrics, and a string keyword. Return true if if the keyword is hidden in the lyrics.

Example 1
```
lyrics = "every breath you take every move you make"
keyword = boom
This should return true because
b is in "breath"
o is in "you"
o is in "move"
m is in "make".
```
Example 2
```
lyrics = "i'm a cowboy on a steel horse i ride, i'm wanted"
keyword = boom
This should return true because
b is in "cowboy"
o is in "on"
o is in "horse"
m is in "i'm".
```

Each word in lyics can only be used for one letter in the keyword. If lyrics = "boom" and keyword = "boom" , then canMatch should return false because there would only be a match for "b".

Next you have to write another function `canMatchAll` that takes a list of lyrics and a list of keywords and returns a hashmap/dictionary where each key is a keyword and each value is a list of lyrics where canMatch returns true. This function must have a running time less than O(length of lyrics * length of keywords).

```py
import bisect
from collections import defaultdict

"""
给lyrics中的字母建立map: character --> index of word
遍历keyword, 通过二分法查找是否有可行解
"""
def canMatch(lyrics, keyword):
    lyrics_words = lyrics.split(' ')
    char_mp = defaultdict(list)
    for i, word in enumerate(lyrics_words):
        for c in word:
            # 注意同个word中有多个相同字母的情况，需要避免重复
            if char_mp[c] and char_mp[c][-1] == i:
                continue
            char_mp[c].append(i)
    
    prev_i = -1
    for c in keyword:
        positions = char_mp[c]
        # 找到第一个 > prev_i 的 postion 的 index
        j = bisect.bisect_left(positions, prev_i + 1)
        if positions[j] <= prev_i:
            return False
        prev_i = positions[j]
    return True

assert canMatch("every breath you take every move you make", "boom")
assert canMatch("i'm a cowboy on a steel horse i ride, i'm wanted", "boom")


def canMatchAll(lyricsList, keywordsList):
    res = defaultdict(list)
    for keyword in keywordsList:
        for lyrics in lyricsList:
            if canMatch(lyrics, keyword):
                res[keyword].append(lyrics)
    return res
```
