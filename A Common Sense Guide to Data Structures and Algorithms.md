---
creation date: 2022-03-15 16:52
aliases: 
tags: 🖥️
---

# [A Common Sense Guide to Data Structures and Algorithms](A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md)
---
By: Jay Wengrow, 2nd Edition

### Chapters
#### Why Data Structures Matter
Code quality is measured in Maintainability and efficiency.
- maintainability encompasses readability, organization, and modularity
- efficiency means time complexity. 
	- We can measure efficiency based on the different types of data structure operations: read, write, insert, delete. 
	- For each, we must think of the best and worst case scenarios

In this chapter, we compare array operations and set operations to make a point that the choice of data structure can have nontrivial implications on code efficiency. 

##### Analyzing efficiency of Arrays
- Reading is *constant time*
	- super efficient because array indexing utilizes  the memory address of the first element, and all remaining elements are in contiguous blocks of memory
- Searching can vary based on length of the array
	- worst case: *linear search* or *N*, were it takes N steps when there are N elements
- Insertion also varies depending on if you are inserting at the beginning, middle, or end
	- Worst case: is *N+1*.
		- inserting at the beginning requires all remaining elements to be shifted over one plus allocating extra memory (since arrays must be contiguous). 
- Deletion is similar to insertion, except we don't need to allocate extra memory for new data.
	- Worst case is *N* to delete the first element and shift all remaining elements over by one.

Array-based Sets, do not allow duplicate values
- Reading is also constant time because of indexing
- Insertion requires a search operation first to ensure the value is not a dupe
	- Worst case is $2N +1$ to insert a new value at the beginning of a set, after searching through N elements, and shifting N elements over by one

#### Why Algorithms Matter
Introduce the power of [Binary Search](./Binary%20Search.md) algorithm compared to linear search.  ^0a3b4f

#### O Yes, Big O Notation

```ad-summary
title: Big O
Big O Notation is a consistent language to describe the time efficiency of data structures and algorithms, in terms of *number of steps relative to N elements*.

❓How does an algorithms performance *change as the data increases*❓
- time complexity doesn't change if data increases --> $O(1)$ 
- if data + 1, time + 1 --> $O(N)$

```

##### $O(log N)$ Explained
*Logarithm* is the inverse of an exponent, and describes how many times a number is multiplied by itself to produce X. 
1
Example: $log_2 64$ --> 64 / 2 / 2 / 2 /2 /2 / 2 = 1, or **6 times** 
$O(log N)$ is shorthand for $O(log_2 N)$.  
$O(log N)$ means the algorithm takes as many steps as it takes to keep *halving the data elements until we remain with 1*.
This is the definition of [Binary Search](./Binary%20Search.md), where we half the data set each time. 

##### Arrays revisited:
Reading an Array - *O(1)*
[Binary search](./Binary%20Search.md) - $O(log N)$ - increases one step each time data is doubled
Linear search - $O(N)$ - "takes N steps for N elements"


#### Speeding Up Your Code with Big O
##### Sorting algorithms
- [Bubble Sort](./Bubble%20Sort.md) - $O(N^2)$ quadratic time complexity

👎Any algorithm that uses nested arrays has $O(N^2)$ quadratic time complexity. These are ripe for optimization!
- Example: determining if an array has duplicate values, we can either:
	- Use nested loops to iterate through each element and the rest of the array OR
	- use Linear search $O(N)$: Iterate once through array and save "seen" elements in second array.

#### Optimizing Code with and Without Big O
Analyze [Selection Sort](./Selection%20Sort.md), which is twice as fast as [Bubble Sort](./Bubble%20Sort.md).
- If we were to be more precise, Selection Sort is really $O(N^2/2)$
- Big O notation drops any "constant" values, hence Big O is a very general generalization, which says selection sort is also $O(N^2)$.
> For Big O, it's like comparing a two-story single family home 🏡 to a 100 floor skyscraper 🌇. They are so different that the number of floors is moot. 

```ad-tldr
title: 🔑 Comparing Algorithms

 Big O is useful for comparing algorithms that belong to different *categories*. When they fall under the *same* classification, further analysis is required (aka total steps).
```

This paradox is why some algorithms can have the "same" efficiency in Big O notation, but in reality have different time complexities.

> If you compared a skyscraper with 50 floors $O(N)$ to a skyscraper to 100 floors $O(2N)$, that's still twice as many floors even though they are both skyscrapers. 😓



###### Examples:
- $O(2N)$ --> is still considered $O(N)$ in Big O, even though it's twice as slow
- This is because ==Big O is meant to describe the trajectory of computational growth when data grows==... they are both still a *linear function*. An exponential function will *always at some point* become slower than $O(N)$ at any factor. 

![Pasted image 20220325154655.png](./images/Pasted%20image%2020220325154655.png)

###### Big O categories:
- linear
- exponential (quadratic)
- logarithmic

![Pasted image 20220510075148.png](./images/Pasted%20image%2020220510075148.png)


#### Optimizing for Optimistic Scenarios
[Insertion Sort](./Insertion%20Sort.md) is $O(N^2 + 2N)$ in the worst case scenario, but we drop both constants and the lower order of N for Big O, which classifies it as $O(N^2)$ just like bubble and selection sort.

So, how should we think about insertion vs bubble vs selection sort? So far, we've analyzed them for the *worst case scenario*, when the array is sorted in reverse order.
- Insertion sort's ==efficiency varies greatly depending on the sort of the starting array==, since there are scenario where only one comparison and no shifts occur. 
	- Best case: $N$
	- Average case: $N^2/2$
	- Worst case: $N^2$
- Selection sort ==doesn't have a mechanism to end the iteration early==, so it will compare every value to the right of the current element, always. 
	- Best case: $N^2/2$
	- Average case: $N^2/2$
	- Worst case: $N^2$

🔑 In reality, we should compare algorithms for the **average** scenario, which has a much higher frequency of occurring than the worst or best case scenario (bell curve, ahem).

Good skills: consider worst, best, and average cases, AND be able to optimize algorithm to cut out unnecessary steps (such as ending a loop early, or refactoring nested loops into one loop)

#### Big O in Everyday Code
This chapter looks at common code examples to practice analyzing the  efficiency of algorithms in terms of Big O.

When an algorithm computes something over an array's elements in a reductive pattern (e.g. the loop is running $N + (N - 1) + (N - 2) + (N - 3)$ ... times), then it has a $O(N^2 / 2)$ efficiency.
- Example: Calculate the product of all combinations of two numbers in an array. 

When a problem uses  *two* primary sets of data, could be $O(N*M)$, which means that the Big O is ==somewhere between $O(N)$ and $O(N^2)$==
- Example: Calculate the product of every number pair across *two* arrays
	- The data set depends on the number of elements in *both* arrays
	- Best case scenario: array 1 has $N$ elements and array 2 has 1 element = $N * 1 = N$, or constant time
	- Worst case: array 1 and 2 have same number of $N$ elements, or $N^2$ efficiency
	- Average  case: array 1 has $N$ elements, and array 2 as $M$ elements, or $O(N*M)$ efficiency

Learn about $O(X^N)$ algorithm, which for every additional element in N, the number of steps double. 
- This is the *opposite* of $O(log N)$! 
- Example: password cracking for a string with length N, we have to loop through all possible combinations of the letters of alphabets (26). So if we are looping through all combinations of 3 letters, that would be $26 *26 *26$, or $26^N$

![Pasted image 20220328133438.png](./images/Pasted%20image%2020220328133438.png)

#### Blazing Fast Lookup with Hash Tables
Also called hash maps or dictionaries, these give us $O(1)$ constant time read efficiency. hash map's are *one directional* -- you need the key to find a value. If you use a value to find a key, you'd have to resort to linear searching all key/value pairs. 

Under the hood, hash maps use a hashing algorithm to hash the key and determine where to put the value.
- a hashing algo can be as simple as numbering letters of alphabet and then finding the product of the key's letters
- a hashing algorithm must return the same result given the same input
- If a collision occurs, say at cell 8, then the computer will store a reference to an array to hold the key/value pairs and use *linear search* to find the right key.  
	-  Using linear search defeats the whole value of hash map, so we should avoid this scenario as much as possible. Most programming languages take care of this implementation for us.

🔑 To preserve the efficiency of, hash maps should optimize to reduce *collisions*, or instances where hashing different keys results in the same memory location. 

Key components to hash map efficiency are:
1. amount of data being stored
2. optimized amount of memory available for the hash map
3. a hash function that will distribute data across all available cells.

📏 Optimized load factor: for every 7 elements, hash table should have 10 cells.

💡While hash maps are used for naturally paired data, we can use a hash map as an index to speed up array searches:
- For any algorithm that requires multiple searches in an array, create a hash map of the array's values to create an index! After this set up, all lookups will be $O(1)$ time, drastically reducing the time complexity. 

> Instead of flipping through every page in textbook, lookup a term in the index and go directly to the page. 📑

#### Crafting Elegant Code with Stacks and Queues
Many data structures are considered *abstract* data structure -- they may be built on top of a built-in data structure like an array, but the ==set of rules and processes around how we should interact with the structure is what makes it unique==. It doesn't matter what structure is used under the hood to implement, just the concept of the rules of the data structure. 

Sets, queues, and stacks are abstract data structures. 

Although Stacks and queues just arrays, they are elegant tools for handling temporary data because of the order enforcement. 
- help prevent potential bugs to solve a problem
- give us a new mental model for tackling problems, e.g. LIFO or FIFO

###### Stacks
[Stacks](./Stacks.md) are LIFO:
1. data can only be inserted and deleted at the end 
2. Only the last element of a stack can be read

How can we use a stack in algorithms?
- a JavaScript linter that checks for missing or mismatched braces/brackets.
- "undo" functionality of a word processor

###### Queue
Queues are FIFO
1. data can only be inserted at the end
2. data can only be deleted from the beginning
3. only the first element at the front of a queue can be read

Queues in action:
- printer that takes in jobs and prints first received
- airplaines taking off
#todo 
- [x] implement a braces Linter as a stack 
- [x] Implement a queue

#### Recursively Recurse with Recursion
Recursion works best in cases where you want to loop for an unknown number of times (e.g. filesystem or node traversal) ^88c3ad

Reading recursive code:
1. Identify the base case
2. Write what the base case evaluates to
3. Go to the case before the base case... so on. 

###### Recursive Category 1: repeatedly execute
Includes: counting down, doubling every number in an array, etc. 

Instead of initializing a variable to keep track of index location in a for loop, pass in extra parameter to the function ! This can be a default parameter.

*Each recursive invocation should pass in a smaller subset of the initial data structure.*

###### Recursive Category 2: Calculations
Performing a calculation based on a subproblem, e.g. factorials, fibonnaci, sum of two numbers, greatest value in an array.

###### Approach to writing recursive algorithms
There are two approaches: bottom up (like a for loop) or top down (which *must* use recursion).

A bottom up approach is similar to using a for loop when you need to start from the first iteration, the data set is changed (like via incrementing), and you pass a transformed value to the next iteration. 

A top down approach relies on abstraction of the problem. How? 🤔
1. Imagine the function you're writing has already been implemented by someone else
2. Identify the next smallest subproblem
3. See what happens when you call the function on the subproblem and go from there 
4. define the base case


```ad-danger

Algorithms that calculate permutations use Factorials, of in Big O, called *Factorial time.* 
==Factorial time is the slowest category of Big O==.  

```

![Pasted image 20220403183545.png](./images/Pasted%20image%2020220403183545.png)

#### Dynamic Programming
Dynamic programming is a technique for optimizing problem by breaking it down into simpler subproblems and storing solution ([Memoization](./Memoization.md)) to repeated sub-problems so they are only solved once.

Chapter goals:
- identify most common  speed traps in recursive code
- how to express algorithms in Big O and fix these problems

##### Speed Trap: Unnecessary recursive calls 
```js
function max(array) {
	if (array.length === 1) return array[0]
	
	if (array[0] > max(array.slice(1, array.length))) {
		return array[0]
	} else {
		return max(array.slice(1, array.length))
	}
}
```

This problem with this code is that we are calling our recursive function twice each iteration since it appears in both the `if` block AND the `else` block.

This is very inefficient, since each call to `max` is a whole call stack of recursive stacks, and in a simple call like `max([1,2,3,4])` we actually call `max[4]` *eight times*. This algorithm is $O(2^N)$ time complexity.

Solution: save the result to a variable. Now, our recursive function calls itself for as many times as there are elements in the array, or $O(N)$

```js
function max(array) {
	if (array.length === 1) return array[0]
	let maxOfRemainder = max(array.slice(1, array.length))
	if (array[0] > maxOfRemainder) {
		return array[0]
	} else {
		return maxOfRemainder
	}
}
```

##### Overlapping subproblems also causes repetitive recursive calls
To find the fibonnaci(N), we need to sum $fibonnaci(N-1)$ + $fibonnaci(N-2)$
We need both values to get the result, but there's tons of overlapping calculations between the two parts.
![Pasted image 20220404105113.png](./images/Pasted%20image%2020220404105113.png)
Since this isn't a single piece of data, storing it into a variable isn't feasible. 

We can use *dynamic programming*, or the process of optimizing recursive problems that have overlapping subproblems. Two common techniques are *[memoization](./Memoization.md)* and going *bottom up*.

###### [Memoization](./Memoization.md)
**[Memoization](./Memoization.md)** reduces recursive calls by remembering previous computed functions by storing them inside a DS like a hashtable. Using this technique, the algorithm should first check if fib(3) has been calculated (hash lookup) before computing it. 
	- In the fib example, we'd store fib(3) = 2 inside `{3: 2}` to remember the result
	- ==Pass the hash table as a second parameter to the function so each recursive call has access to it!==

Refactored fibonnaci using [memoization](./Memoization.md) each call in the black box represents a call in which the result was retrieved from the hash table. This produces $O(N)$ Big O time!

![Pasted image 20220404105950.png](./images/Pasted%20image%2020220404105950.png)

```js
function fib(n, memo={}) {
  if (n== 1 | n===0) return n
  if (!memo[n]) {
    // save calculation to memo hash table
    memo[n] = fib(n - 1, memo) + fib(n-2, memo)
  }
  return memo[n]
}
```

###### Bottom Up
This technique does *not* use recursion, it uses loops. For the fibonnaci algorithm, we start from the bottom (fib(0) and fib(1)) and iterate up to $N$.

#### Recursive Algorithms for Speed
❓Why are sorting algorithms so important? 
🔑 There are many algorithms that use sorting as a component of the larger process. The fastest sorting algorithms are $O(N * log N)$, like [Quick Sort](A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md#Quick%20Sort) or merge sort, so we know that algorithms that employ sorting will have a baseline efficiency of $O(N * log N)$.

For example. finding duplicates in an array is much more efficient when we presort the array.

##### Quick Select
**Quick select** algorithm is useful for finding the "kth element" in an array *without sorting the entire array*. It is a hybrid of [Quick Sort](./Quick%20Sort.md) and [Binary Search](./Binary%20Search.md), since it uses partitioning. 

Quick select is $O(N)$ in Big O notation.
- For $N$ elements, we would need $N + N/2 + N/4 + N/8$... steps, which reduces to $2N$

Basically, we can "throw away" half of the array since we know we're only looking for a specific element, and partitioning guarantees higher/lower buckets relative to the pivot that ends up somewhere in the middle. It's implementation is very similar to quick sort:

1. Base case is if the subarray has 1 or 0 elements, we've found our element
2. Partition the array and obtain the index location of the pivot
3. If kth element < pivot index, partition *only* the left half of the subarray
4. else if kth element > pivot index, partition *only* the right half of the subarray
5. We stop when kth == pivot index!

#### Node-Based Data Structures
Nodes are pieces of data that are dispersed throughout the computer's memory. Node-based data structures offer new ways to organize and access that data with some performance advantages (read: tradeoffs!)

##### [Linked List](./Linked%20List.md)
[Linked lists](./Linked%20List.md) solve for the disadvantage of arrays:  arrays require *contiguous* blocks of memory.
- Nodes (or items) in a linked list are *scattered throughout computer's memory*
- Each node stores both the *data AND a pointer (memory address) to the next node*. 
- Linked list only needs to know the location of the head (first node)

###### Performance compared to Arrays
Linked lists best cases are complete opposite of arrays
- 👍 Scale better than arrays
- 👍Insertion & Deletion at *beginning* is $O(1)$ and  $O(N)$ *at end of list*
- 👎 Reading and searching from linked list is $O(N)$ compared to array's $O(1)$ read time. Search times are equivalent.
- 👎Each node has two blocks, therefore linked lists require twice the amount of memory allocation compared to arrays.

```ad-summary
title: 🔑 Linked lists > arrays 

Linked lists are efficient when you are inserting/deleting as you search through it, since *no shifting needs to happen*, we are just updating pointers! 

Insertion & deletion in arrays require up to $N$ elements to be shifted after an insert/delete operation if it happens at the beginning/middle of array.

```


![Drawing 2022-04-12 linked list.excalidraw](./images/Drawing%202022-04-12%20linked%20list.svg)


##### Doubly Linked Lists
Nodes in a [doubly linked list](./Doubly%20Linked%20List%20Code%20Implementation.md) has two links-- one that points to the next node, and another that points to the previous node. 

Doubly linked lists keep track of the head and the tail of the list, which gives it an edge:
- 🌟 Insertion/Deletion, and Reading from beginning *and* end is $O(1)$

This makes them the perfect data structure for **[queues](./Queue%20Code%20Implementation.md)**! 

![Drawing 2022-04-12 doubly linked lists.excalidraw](./images/Drawing%202022-04-12%20doubly%20linked%20lists.svg)

#### Speeding Up All the Things  with Binary Search Trees
Ordered arrays are effective data structures since they get to use the super power that is [Binary Search](./Binary%20Search.md) at $O(log N)$ time instead of the crappy linear search $O(N)$ (plus  constant look up time!) 

🤔How do we keep an array sorted with each insertion efficiently?
-  Ordered arrays are relatively slow at since the array must be shifted $N$ times for $N$ number of elements that are greater than the inserted element, or $O(N)$ time complexity. 

Hash tables have great search, deletion, and insertion time, at a whopping $O(1)$ -- but they don't hold any order! 😲 

Fortunately, we can have our cake and eat it too with **binary search trees** 🍰

##### [Binary Search Tree](./Binary%20Search%20Tree.md)
*Trees* 🌳 are node-based data structures. Each node can have links to *multiple nodes*. A tree is balanced when its nodes' subtrees have the same number of nodes in it. A [binary search tree](./Binary%20Search%20Tree.md) ✌️🌳is a tree in which each node has 0, 1, or two children and *is ordered*.

A [binary search tree](./Binary%20Search%20Tree.md) ✌️🙈🌳  has two requirements:
1. each node can have *at most* one "left" child and one "right" child
2. A node's "left" descendants can only contain values that are *less than the node itself*. A nodes' "right" descendent can only contain values that are greater than node itself. 

**Balanced [binary search tree](./Binary%20Search%20Tree.md)**
![Pasted image 20220413160831.png](./images/Pasted%20image%2020220413160831.png)

###### Efficiency
Searching binary tree and sorted array are the same $O (log N)$.
A balanced binary tree really shines with *insertion*, which is $log N$ time, beating out insertion in an order array! 😍😍
 
###### Searching binary tree :
- essentially a pseudo-sorted tree, since it guarantees "greater" and "lesser" sides of the tree at each level
- uses binary search, since *each step eliminates half of the remaining nodes from the search*. This is the definition of $O(log N)$! 🔆
- Each time we add a level to the tree, we ~double the number of nodes.
	- said differently, a tree with $N$ nodes needs $log N$ levels, since each step of the search causes us to move down a level -- we take up to as many steps are there are levels in the tree. 

###### Insertion into balanced binary tree ⭐
Insertion always takes just one step beyond a search (e.g. getting to the bottom of the tree), which means it's always $log N + 1$ steps, or just $log N$! 

Arrays will always be $O(N)$ for insertion because of the shifting that needs to happen. 

###### Unbalanced binary search trees
Binary search trees lose their efficiency when the become unbalanced. The worst case is when the tree is completely linear. Searching and insertion would take $O(N)$ 😔

Ironically, the tree ends up linear when we insert values from an *ordered array* `[1, 2, 3, 4, 5]`. 

![Pasted image 20220413174321.png](./images/Pasted%20image%2020220413174321.png)

```ad-tip
title: Randomization is your friend

If you are converting an ordered array into a binary search tree, make sure to randomize array first. 

```
###### Deletion
Delete is still $O(logN)$, but the algorithm is complex and recursive 😖
- If we delete a node that has no children, we just simply delete.
- If we delete a node that has one child, replace the child in the delete node's spot.
- If we are deleting a node that has *two children nodes*, we have to find the *successor node* to replace the node. ==The successor node is the *next largest value* in the tree relative to the deleted node.== 

**Finding successor node**
![Pasted image 20220413180141.png](./images/Pasted%20image%2020220413180141.png)
- Visit the *right* child of the deleted value, and then keep visiting the left child. This gives us the *next largest* value of the delete value. 
- if the successor node has a right child, then it becomes the *left child* of the former successor's parent node.  
- ![Pasted image 20220413180426.png](./images/Pasted%20image%2020220413180426.png) 

##### Traversing Binary Search Trees
We have to visit every node, so traversal by definition $O(N)$. 

Ways of traversing:
1. *Inorder traversal* (means to traverse a tree in ==alphabetical or ascending order== (left-root-right)
2. *Preorder traversal* means to go from root-left-right, or in the order that the items were added to the BST. This is used to ==create a copy of the tree==.
3. *Post order traversal* means to go through the all nodes at each level, starting with lowest level first (left-right-root). This method is used to ==delete the tree==.

###### Inorder Algorithm (Search BST):
🍃 Traverse left subtree first 
- Recursively traverse a node's left child, until there are no more left children (you've found the first node in the order)
- "visit" the node 
- call itself (traverse) recursively on the node's right child until hit a node that doesn't have a right child (you're found the next largest value, aka second in order)
- Base case is when traverse is called on a child that doesn't exist

Traversal starts with the root node, but the "find" pattern is to go all the way to the left bottom most node
![Pasted image 20220413191931.png](./images/Pasted%20image%2020220413191931.png)

###### Preorder Algorithm (copy BST)
🍃 Root then down the left subtree 
1. Visit the root
2. Traverse left subtree
3. Traverse right subtree

###### Postorder Algorithm (delete BST)
🍃 Start from bottom, going up one layer at a time 
1. Traverse the left subtree
2. Traverse the right subtree
3. Visit the root

💻[Implementation Code](https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/)

#### Keeping Priorities Straight with Heaps
##### Priority Queues
A Priority queue is like a queue with two additional constraints:
- Data is inserted to preserve queue in a specific order.
- Data can only be deleted from the front of the queue (the priority that gets taken care of).

🚑 Example: emergency room triage queue

If we used an ordered array to implement a priority queue:
- We always delete from the end of the array, so this is $O(1)$
- Insertion in order means we need to check all of the elements to determine where to insert the data, or $O(N)$

This isn't great performance for insertions -- enter Heaps. 

##### Heaps
Heaps are great for implementing a priority queues since the root node is the "priority" node, and we can *instantly access* the root of a heap.

*Binary heap* is a specific kind of binary tree, and it can be either a *max heap* or *min heap* (very similar concepts). 

Heap requirements:
1. *Heap condition:* value of each node must be greater than each of its descendant nodes
2. Tree must be *complete* (each row of the tree is completely filled with nodes)
	- Note: the bottom row can have empty nodes as long as there isn't a node to the right of it

Completeness is important to a heap because it ensures it is well-balanced, which preserves quick traversal (e.g. doesn't end up looking  [unbalanced](A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md#Unbalanced%20binary%20search%20trees))

Valid binary max heap:
![Pasted image 20220414151142.png](./images/Pasted%20image%2020220414151142.png)

👎 Not great at searching because heaps are *weakly ordered*.  Unlike a [binary search tree](./Binary%20Search%20Tree.md), its requirements doesn't help you search the tree (e.g. left or right subtree )-- you just know descendants are less than the ancestor.  
👍 Heaps are *very fast* at **insertion** and **deletion**. 
👍Great for priority queue implementation

###### Heap Insertion
1. Insert new node as **the last node** (e.g. the next available rightmost spot in the bottom level)
2. Compare node with parent node
3. If node > parent, swap.
4. Repeat trickling up until node has a parent that is greater than it.

Efficiency of insertion is $logN$
- The maximum number of steps is equal to the number of levels in the tree, and we know there are $log N$ levels for  $N$, number of nodes, 

###### Heap Deletion
***Only the root node is deleted.*** 
1. Move the ***last node*** into where the root node was.
2. Trickle the root node down until there are no children greater than it.
	1. Check both children nodes.
	2. Swap the trickle node with the *greater of the two children nodes* to keep a valid heap
	 
###### Finding the last node is critical to heaps efficiency
Insertion and deletion rely on efficiently finding the *last node*. In a binary tree, it's difficult to find the last node without visiting each node.

💫 We can ==use arrays to implement a heap== since they have instant access to the last element in the list, which is the last node.

**[Heap as an Array](./Heap%20Implementation.md):**
- Weak ordering of nodes is based on the tree level
- Instead of using linked nodes to traverse the tree, we can use calculations on the index:
	- Finding left child of any node: $(index *2) + 1$
	- Finding right child of any node: $(index *2) + 2$
	- Finding parent of any node using integer division: $(index -  1)/2$
- Insert an element the end of the array (after the last node) 

![Pasted image 20220414155704.png](./images/Pasted%20image%2020220414155704.png)
##### Heaps vs Ordered Arrays
Heaps are consistently faster in efficiency, therefore a better choice when data is changing.
| Operation | Ordered Array | Heap    |
| --------- | ------------- | ------- |
| Insertion | $O(N)$         | $O(logN)$ |
| Deletion  |$O(1)$          | $O(logN)$ |

Heaps give us instant access to the priority node, and it's also the that gets removed.  The next-highest item floats the top of the heap and "on deck". 

💡The *weak ordering* of the heap is what gives it it's advantage. Since it is not perfectly ordered (like a sorted array), we can insert at $O(log N)$ time. It is ordered enough so that we can instantly access the priority/root.

#### It Doesn't Hurt to Trie
A *[trie](./Trie%20Implementation.md)* is a tree that is a collection nodes that point to other nodes.
- can contain any number of children nodes, unlike binary tree
- a common implementation: node is a hash table in which keys are letters and values reference other node hash tables
- ideal for text-based features, like searching a dictionary for autocomplete.
- requires a terminating node somewhere in the lineage to denote a "complete" word

Tries main operation is search -- we can search to determine if a word is a complete word or whether a string is a word prefix. 

###### Searching Tries :
- Starting with the root node, iteratively check children nodes with a key of the letter we're searching for.
- If found, make child node current node
- If we reach the end of the string, the word exists. 

![Drawing 2022-04-14 trie.excalidraw](./images/Drawing%202022-04-14%20trie.svg)

✨ Tries have *amazing* look up time -- $O(K)$, or *almost* constant

Trie's aren't $O(N)$ and they aren't quite $O(1$) -- they are $O(K)$
- Each letter takes O(1) steps, because lookup in hash tables are constant time 
- Tries take as many steps as there are letters to search, so it's not accurate to say constant time.
- Searching in Tries depend on length of the word, but each additional letter does *not* *increase the number of nodes* that need to be searched (the number of words in the dictionary stays the same, no matter how short or long the word is), which is what $O(N)$ means
	- Most non-constant algorithms are tied to the amount of data -- as N increases, the algorithm slows down. O(K) algorithm means *no matter how large the trie is*, searching for the word "cat" will *always take three steps*

###### Insertion Tries
Similar to searching:
1. Loop over each letter in the word
2. Start with the root node, check if current node has a child key with current letter
3. If it doesn't, add the child node with current the letter key
4. Repeat until reach the end of the string
5. Add a `*` child to the last node to indicate the word is complete.

We use trie's search and insertion methods to create an [Autocomplete feature](./Trie%20Implementation.md)

#### Connecting Everything with Graphs

##### All trees are a graph, not all graphs are trees. 📉
1. Graphs can have *cycles*, or nodes that connect to each other in a circular reference.
2. Graphs do *not* have to be fully connected

Example: social network 👭💬 where we represent "friendships"

 Graphs have "verticies" (nodes) that can be connected be an "edge", or "adjacent" to each other (neighbors). A graph where all vertices are connected in some way is a *connected graph*. 

In a connected graph, all we need is access to one vertex to be able to access the rest. In an unconnected graph, it's not possible to discover all vertices from one vertex and may nee to store all the graph vertices in additional data structure like an array.

There are many different ways to implement a graph.
1. *Adjacency List* implementation uses an a list to store a vertex's neighbors (scope of this chapter). 
2. *Adjacency Matrix* uses 2D arrays (not discussed in book. )

We can use hash tables to represent a basic graph and get constant time look up of a person's friends.
```js
friends = {
	"audry": ['bobby','molly', 'abby'],
	"bobby": ['rudy', 'duffy', 'seb'],
}
```

##### Directed graphs can represent non-mutual relationships
Audry can follow Bobby and Molly, but Bobby and Molly may not follow Audry. 
- In a mutual relationship, whenever we add one neighbor to a vertex's list of neighbors, we can also automatically add the vertex as a neighbor to the neighbor's list of neighbors. 
- In a non-mutual relationship, we wouldn't automatically do this

##### Searching graphs
Search can mean finding a particular vertex from the current vertex, verifying if two vertices are connected, or traversing every vertex from A to B.

Key is to keep track of which vertices have already been visited, since graphs can be cyclical (and get stuck in an infinite loop ♾️)

Common way is to use a hash table to store visited vertices.

###### Depth First Search (DFS)
Recursive algorithm searches as far away
Similar to [binary search tree traversal](A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md#Traversing%20Binary%20Search%20Trees)
Example: filesystem traversal 

1. Start at any vertex
2. Iterate through vertex's list of neighbors
3. If neighbor vertex has been visited (check hash table), ignore
4. Else, recursively perform depth first search on neighbor's adjacent vertices. Make sure to add neighbor to "visited" hash table

###### Breadth First Search (BFS)
Uses queue to implement FIFO algorithm to decide the order of vertices to visit. In BFS, the first vertices in are the "closest" neighbors, and continues it's search by "spiraling outward" from the starting node

1. Start with any vertex in the graph
2. Mark vertex as "visited" by adding to hash table
3. Enqueue vertex
4. While queue isn't empty, dequeue first vertex in queue as the "current vertex"
5. Iterate over current vertex's neighbors
6. If neighbor is visited, skip it
7. Else, mark neighbor as "visited" and add it to the queue
8. Repeat until queue is empty 

###### DFS vs BFS
Depends on your use case. 
▶️ Use breadth-first when you want to find the closest connections first (horizontal)
- e.g. finding all of Audry's direct connections in a social network
▶️ Use depth-first when you want to find a vertex that is likely far away from the starting vertex
- e.g. finding a great-grand child in a family tree

###### Efficiency of graph searches
Worst case scenario in either is that we are traversing the entire graph, or searching for a vertex that doesn't exist. 

For either type of search, the main operations are 1) visiting each vertex (V) and 2) visiting each of the vertex's adjacent vertices (e.g. following an edge), expressed as:

$O( V + E)$ 

Note that depending on the shape of the graph, an edge may be used more than once (e.g. Audry is a neighbor of Bobby, and Bobby is a neighbor of Audry.). 

 In these cases, we can be more precise and say Big O is really more like $O( V + 2E)$, but we drop constants anyways.  

First graph has **21 steps** = 5 vertices + 16 iterations over adjacent neighbors
![Pasted image 20220416232759.png](./images/Pasted%20image%2020220416232759.png)
Second graph has **13 steps** = 5 vertices + 8 iterations over adjacent neighbors
![Pasted image 20220416232808.png](./images/Pasted%20image%2020220416232808.png)

There special graph databases that can store relationship data, like Neo4j. 

##### Weighted Graphs
Weighted graphs specify a weight or value for each edge.
Example: map of cities and their distances from each other,[ flight routes with flight prices](./Dijkstra%20Algorithm.md) 🛫

With weighted graphs, we're often interested in the shortest possible path to get from vertex A to B, where the "shortest" path is calculated based on weight (distance) values of the edges. In a graph of flight prices, the "shortest" path may mean finding the cheapest flight route from A to B.

In our flight route example, we can use Dijkstra's algorithm to find the cheapest flight path from Atlanta to every other city, not just the city we're interested in! 

###### Dijkstra's Algorithm (Weighted Graph)
[Dijkstra's Algorithm ](./Dijkstra%20Algorithm.md)Is one of the most famous ones to solve the shortest path problem. It finds the shortest path from a starting point to *all known* vertices, even if we are only interested in one particular vertex.

At a high level, we visit each vertex. When we visit the current vertex, we iterate through the current vertex's *adjacent* vertices, calculating the cost to get from the **start vertex** to the current vertex's **adjacent vertices**. Because we're calculating the cost to each of the adjacent vertices, we can visit the adjacent vertex with the *total shortest travel cost*. Every iteration we choose the lower cost option and track the path, updating the shortest path as we continue to visit vertices.

**Set up:**
1) hash table that tracks "costs" to travel from **starting vertex** to any other vertex
2) hash table that tracks vertex (value) immediately *before* a given vertex (key) that represents the shortest path to get to given vertex.
3) hash table of "visited" vertices
4) queue of unvisited vertices ([|priority queues](A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md#Priority%20Queues) would be perfect for this use case, since we can designate the vertex with the shortest "cost" from our **starting vertex**as the priority item)

**Algorithm**
1. Visit the starting vertex as the "current" vertex 
2. For each of the **current vertex's** adjacent vertices, calculate the cost of travelling from the **starting vertex** to the **adjacent vertex**.
3. If the cost to travel to **adjacent** vertex from the **starting** vertex is *lower* than what's recorded in the `cost` hash table (or we haven't tracked a cost for this adjacent vertex):
	1.  update the table to reflect the lower cost
	2. update the `cheapestParent` hash table, like so `{ adjacentVertex: currentVertex }`
4. Visit the next unvisited city that has the *lowest cost from **starting vertex*** and make it the current vertex.
5. Repeat 2 - 4 until there are no more unvisited vertices.
6. Look up the shortest path from **starting vertex** to **destination vertex**. in the `costs` hash table
7. Using a loop, work backwards from **destination vertex** to iteratively look up the `parent` vertex that will provide the shortest path.


###### Shortest Path for Unweighted Graph
We can utilize the same concepts to [find the shortest path (e.g. least number of vertices travelled) from A to B in an unweighted graph](./Shortest%20Path%20(Unweighted%20Graph).md). It's actually a bit simpler.

Use a combination of the Dijkstra's algorithm and [|breadth first search](A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md#Breadth%20First%20Search%20BFS). Since we want the shortest path, searching all of the *nearest* adjacent vertices first guarantees that we will find the shortest path, and we can break out of the iteration early once we find the target value. 


#### Dealing with Space Constraints
Measuring the amount of memory an algorithm consumes (space complexity) is another way to measure efficiency, and we can do so in Big O notation.

❓ Key question: *if there are N data elements, how many units of ==additional memory== will the algorithm consume?*

We care about extra space the algorithm will consume, not factoring in the memory used at the start (e.g. an initial data structure). If our algorithm creates a new array after iterating through the input array, it will have $O(N)$ space complexity. The same algorithm that mutates the input array in place has $O(1)$ space complexity. 

🔑 Balancing space and time constraints are all about trade offs and your use case.

##### Space constraints with Recursion
Each recursive call adds a function to the call stack, or $O(N)$ space compelxity. 

Function calls (and all of it's temporary variables created) are stored in contiguous blocks in the *stack memory*, a temporary storage area which will be released after call stack if complete. Stack memory is managed by the OS, and memory is automatically allocated and deallocated. When we run out, we get a *stack overflow*. 

For each recusive call, we are adding another frame on the stack, and those cannot be released until we reach the base case. 

Global variables and objects/arrays are stored in *heap memory*, based on the executed code written by the programmer.  

#### Techniques for Code Optimization
1. Identify Big O of current algorithm
2. Determine the best imaginable Big O
3. If there's a difference, we can optimize

It's not always possible to achieve the best imaginable Big O, but perhaps you can optimize to a speed that is somewhere in between.

##### Magical Lookups
❓ Would my algorithm benefit if I was able to *instantly* look up a piece of data?
- Often times, a hash table can be used to check if something "exists" or "remembering" a value
- Any algorithm that seems to loop multiple times through the same array is likely ripe for a magical lookup!

Examples:
- Two Sum problem -- use a hash table to look up the "counterpart"
- [Sum Swap from two arrays](file:///home/ahsu/projects/dsa/sum_swap.js) - swap a number from each array so that the sum of both arrays equals same number
	- Use pattern recognition to realize that the "target" sum is the midpoint between the current sum of two arrays
	- Find the counterpart in the second array that results in the "target" sum

##### [Greedy Algorithms](./Greedy%20Algorithms.md)

##### Change Data Structure
If order doesn't matter, we can use a hash table to store strings in a hash table instead of an array to count instances.

---
Tags: [Programming](Programming.md) - #capstone- [Capstone Prep](./Capstone%20Prep.md)

Reference:

Related: 