---
creation date: 2022-03-22 14:34
aliases: 
tags: 🖥️
---

# [Linked List](Linked%20List.md)
---
Linked lists are a [|node based data structure](./A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md#Node-Based%20Data%20Structures).

📒[Notes](https://3.basecamp.com/3695031/buckets/26635906/documents/4929838675)
- Nodes hold reference to next node only
- Head points to the first node in the list, and `null/nil` ends the list.
- typical to use `prev` variable to keep reference to previous node if two pointers needed
- "k pointer slide" is a very common mental model
- three pointers: use `prev`, `current`, `next` ( or `current.Next` is sometimes easier to prevent going past the end of the linked list)
⭐ Insertion & deletion are O(1) > arrays O(N)

👀 Edge cases: removing first or last node 

###### Traverse a list 
- Adjust pointers while traversing
- Modify in place, or accumulate into a var or some data structure (a counter, another LL, hash table, etc)
```go
if head == nil {
  return head // special case
}

current := head
for current != nil {
  // do something
  current = current.Next
}
return head
```

###### Dummy nodes
Sometimes, the use of a dummy node that points to the head of the linked list makes it easier to handle edge cases when the beginning of the linked list is being modified.
- `prev` points to dummy head, and `current` points to the head. 
- if the `head` needs to be removed from the linked list, `current` can move to the next node and `prev.Next` can continue pointing to new current `current`.
- Except for above edge case when head needs to be removed from the list, dummy node helps keep reference to head and we can return `dummy.Next` to return our resulting linked list 

Examples: [Remove element from linked list](https://leetcode.com/problems/remove-linked-list-elements/submissions/)
![Drawing 2022-05-15 20.50.37_linkedlist_kpointers.excalidraw](./images/Drawing%202022-05-15%2020.50.37_linkedlist_kpointers.svg)


###### Implementation
```js
class Node {
  constructor(data) {
    this.data = data
    this.next = null
  }
  nextNode() {
    return this.next
  }
}

let node1 = new Node('once')
let node2 = new Node('upon')
let node3 = new Node('a')
let node4 = new Node('time')
node1.next = node2
node2.next = node3
node3.next = node4

class LinkedList {
  constructor(firstNode) {
    this.head = firstNode
  }
  read(index) {
    let current_index = 0
    let current_node = this.head

    while (current_index < index) {
      current_node = current_node.nextNode()
      if (current_node === null) return null // reached end of list
      current_index++
    }
    return current_node.data
  }

  indexOf(value) {
    let current_node = this.head
    let current_index = 0

    while (current_node.data !== value) {
      current_node = current_node.nextNode()
      if (current_node=== null) return null
      current_index++
    }
    return current_index
  }

  insertAtIndex(index, value) {
    const newNode = new Node(value)

    if (index === 0 ) {
      newNode.next = this.head
      this.head = newNode
      return
    }
    let current_index = 0
    let current_node = this.head

    while (current_index < index - 1) {
        current_node = current_node.next
        current_index++
    }

    newNode.next = current_node.next
    current_node.next = newNode
    return

  }

  deleteAtIndex(index) {
    if (index ===0 ) {
      this.head = this.head.next
      return
    }
    let current_index = 0
    let current_node = this.head

    while (current_index < index - 1) {
      current_node = current_node.next
      current_index++
    }
    const nodeAfterNext = current_node.next.next

    // node to delete is last node
    current_node.next = nodeAfterNext === null
      ? null
      : nodeAfterNext
    return
  }

  readAll() {
    let current_node = this.head
    while (true) {
      if (!current_node) break
      console.log(current_node.data);
      current_node = current_node.next
    }
  }

  last() {
    let current_node = this.head
    while (true) {
      if (!current_node.next) break
      current_node = current_node.next
    }
    return current_node
  }
  reverse() {
    let previous_node = null
    let current_node = this.head
    let nextNode

    while (current_node) {
      nextNode = current_node.next
      current_node.next = previous_node

      previous_node = current_node
      current_node = nextNode
    }
    this.head = previous_node
  }

  // only works if node is not the last node
  deleteNode(node) {
    let current_node = node
    let nextNode = current_node.next

    if (!nextNode) return

    current_node.data = nextNode.data
    current_node.next = nextNode.next

  }
}

let list = new LinkedList(node1)
```

---
Tags: [Programming](Programming.md) - [Go Lang](./Go%20Lang.md) - [Data Structures](Data%20Structures.md)

Reference:

Related: [A Common Sense Guide to Data Structures and Algorithms](./A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md)