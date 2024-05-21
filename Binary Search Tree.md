---
creation date: 2022-04-14 18:58
aliases: 
tags: 🖥️
---
## [Binary Search Tree](Binary%20Search%20Tree.md)
Recursive Definition:
- all left children < root
- all right children > root 
- left subtree and right subtree are valid BST, AND
- each node satisfies: min < `node.Val` < max values

![Pasted image 20220518214037.png](./images/Pasted%20image%2020220518214037.png)
![Pasted image 20220518214045.png](./images/Pasted%20image%2020220518214045.png)
###### DnC template for BSTs:
```go
func someProblem(root *treeNode) *treeNode {
  // base case -- usually root is nil

// divide
  left, right := root.Left, root.Right
  // conquer (recurse)
  leftResult := someProblem(left)
  rightResult := someProblem(right)

  result := // combine root, leftResult and rightResult
  return result
}
```

Tip for solving BST problems:
- Recursive is easiest, because BSTs are recursive structures in nature 
- Use the definition of the problem in the solution! 

Leetcode: 📃Trees List Problems

##### Tree Traversal Orders
Talked about [Ways of traversing BSTs](./A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md#Traversing%20Binary%20Search%20Trees)

Depth First Search
- 🤩 Recursion is easiest (Preorder, Inorder, Post order)
- Iteration with a **Stack** (don't recommend)
	- go to left node, but put right child on stack first so you don't lose reference to right side of tree

Breadth First (Level Order Traversal)
- must use a **Queue**
	- dequeue node, then add children (left, then right) to queue
T: O(n)
S: O(n)

###### Implementation
```js
class TreeNode {
  constructor(value, left=null, right=null) {
    this.value = value
    this.leftChild = left
    this.rightChild = right
  }
}

let node1 = new TreeNode(25)
let node2 = new TreeNode(75)
let root = new TreeNode(50, node1, node2)

const searchBtree = (value, node) => {
  if (!node || node.value === value) {
    return node
  }

  if (value > node.value) {
    return searchBtree(value, node.rightChild)
  } else {
    return searchBtree(value, node.leftChild)
  }
}

const insertIntoBtree = (value, node) => {
  if (value > node.value) {
    // console.log('inserted to the right child of ', node.value);
    if (!node.rightChild) node.rightChild = new TreeNode(value)
    else {
      return insertIntoBtree(value, node.rightChild)
    }
  } else if (value < node.value) {
      if (!node.leftChild) {
        // console.log('inserted to the left of ', node.value);
        node.leftChild = new TreeNode(value)
      } else {
        return insertIntoBtree(value, node.leftChild)
      }
    }
  }

const deleteFromBtree = (valueToDelete, node) => {
  // base case is reached if at bottom of tree
  if (node === null) return null
  if (valueToDelete < node.value) {
    // reassign delete node's left child to deleted node
    node.leftChild = deleteFromBtree(valueToDelete, node.leftChild)

    // return current node to be used as new value of it's parent left or right child
    return node
    
  } else if (valueToDelete > node.value) {
    node.rightChild = deleteFromBtree(valueToDelete, node.rightChild)
    return node
    
  } else if (valueToDelete === node.value) {
    // case 1: node has only one child
    if (!node.leftChild) {
      return node.rightChild
    } else if (!node.rightChild) {
      return node.leftChild

    // case 2: node has two children
    } else {
      node.rightChild = lift(node.rightChild, node)
    }
  }
}

// recursively find successor node in left subtree
function lift(node, nodeToDelete) {
  if (node.leftChild) {
    node.leftChild = lift(node.leftChild, nodeToDelete)
    return node
  }
  // if no left child, current node is the successor
  // make current node take on values of notde to dleete
  nodeToDelete.value = node.value
  // successor's right child becomes parent's left child
  return node.rightChild
}

// Inorder Traversal
const traverse = (node) => {
  if (!node) return
  traverse(node.leftChild)
  console.log(node.value);
  traverse(node.rightChild)
}

// find greatest value in Btree
const greatestInBtree = (node) => {
  if (!node.rightChild) return node  return greatestInBtree(node.rightChild)
}
```
###### Inorder Traversal  (Go)
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func inorderTraversal(root *TreeNode) []int {
    res := []int{}
	var helper func (root *TreeNode)

    helper = func(root *TreeNode) {
	if root!= nil {
        helper(root.Left) // visit left subtree first
        res = append(res, root.Val)  // then node
        helper(root.Right) // then right subtree
    }
}
	helper(root)
    return res
}

```
###### Postorder Traversal (Go)
```go
func postorderTraversal(root *TreeNode) []int {
    var helper func (root *TreeNode)
    res := []int{}
    
    helper = func(root *TreeNode) {
	if root!= nil {
        
        helper(root.Left) // visit left subtree first
        helper(root.Right) // then right subtree
        res = append(res, root.Val) // visit root node last
    }
}

	helper(root)
    return res
}
```
###### Preorder Traversal (GO)
```go
func preorderTraversal(root *TreeNode) []int {
    var helper func (root *TreeNode)
    res := []int{}
    
    helper = func(root *TreeNode) {
	if root!= nil {
        res = append(res, root.Val) // root first
        helper(root.Left) // then left
        helper(root.Right) // then right
    }
}

	helper(root)
    return res
}
```

