## Cord Tree
https://leetcode.com/discuss/interview-question/1593355/Google-or-Phone-Interview-or-Rejected

A cord tree is a binary tree of strings.
A node in this tree can be a leaf node or an internal node.
An internal node has two children, a left child and a right child. It also has a length of all the children under it
A leaf nodes have a value and a length
```
                                #      InternalNode, 26
                                #      /              \   
                                #     /                \                         
                                #    /                  \
                                # Leaf(5, ABCDE)      InternalNode, 21
                                #                       /           \
                                #                      /             \
                                #                     /               \
                                #                    /                 \
                                #         Leaf(10, FGHIJKLMNO)     Leaf(11, PQRSTUVWXYZ)  
```
Q1: Define a Data Structure that represents a Cord tree.

Q2: Define a function that takes in a tree and an index and returns the character at that index.


## Long string
https://leetcode.com/discuss/interview-question/413991/

