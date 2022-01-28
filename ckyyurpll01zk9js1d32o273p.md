## Trim a binary search tree

Well, well, well
I know what you all are thinking. A tree problem??

When are we ever gonna use it? Why do interviewers ask questions related to Trees?

## My thoughts
It is not about a Tree question. It is about checking your logic, how confident are you with recursion. The best and the worst thing about a Tree problem, is that it looks intimidating. But, trust me on this, when you actually get the hang of recursion, code will start flowing in front of your eyes, you will start seeing the branches, the function call stack.

Having said all this, shall we begin?

## Problem statement
Given the root of a [binary search tree](https://www.geeksforgeeks.org/binary-search-tree-data-structure/) and 2 numbers min and max, trim the tree such that all the numbers in the new tree are between min and max (inclusive). The resulting tree should still be a valid binary search tree.

Let us take this tree for an example:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643362223674/XOdo3ooMp.png)

and we’re given **min value as 5 and max value as 13**, then the resulting binary search tree should be:


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643362264243/RZ7QQBAQb2.png)

## Solution
**We should remove all the nodes whose value is not between min and max**. We can do this by performing a **post-order traversal** of the tree. We first process the **left** children, then **right** children, and finally the node itself. So we form the new tree bottom up, starting from the leaves towards the root. As a result while processing the node itself, **both its left and right subtrees are valid trimmed binary search trees** (may be null as well).

If current node’s value is between min and max (min<=node.value<=max) then there’s no action need to be taken, so we return the reference to the node itself. 

If current **node’s value is less than min**, then we **return the reference to its right subtree, and discard the left subtree**. This is because if a node’s value is less than min, then its left children are definitely less than min since this is a binary search tree. But its right children may or may not be less than min we can’t be sure, so we return the reference to it. 
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643394610104/1kbIcPUKl.png)

Similar situation occurs **when node’s value is greater than max**, we now **return the reference to its left subtree**. Because if a node’s value is greater than max, then its right children are definitely greater than max. But its left children may or may not be greater than max. So we discard the right subtree and return the reference to the already valid left subtree.
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643394955527/nbKCgcHkK.png)

## Code
The code here will now be easier to understand:

**In Java:**
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643395532426/OvOWGq9k2.png)
**In C++:**
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643395461387/KN2KpsWDz.png)



