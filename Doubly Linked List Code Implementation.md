---
creation date: 2022-04-13 15:43
aliases: 
tags: 🖥️
---

###### Description [Doubly Linked List Code Implementation](Doubly%20Linked%20List%20Code%20Implementation.md)
[Doubly Linked Lists](./A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md#Doubly%20Linked%20Lists)

###### Implementation
```js
class Node {
  constructor(data) {
    this.data = data
    this.previous = null
    this.next = null
  }
}

let node1 = new Node('once')
let node2 = new Node('upon')
let node3 = new Node('a')
let node4 = new Node('time')

node1.next = node2
node2.next = node3
node2.previous = node1
node3.next = node4
node3.previous = node2
node4.previous = node3

class DoublyLinkedList {
  constructor(firstNode, lastNode) {
    this.head = firstNode
    this.tail = lastNode
  }
  read(index) {
    let current_index = 0
    let current_node = this.head

    while (current_index < index) {
      current_node = current_node.next
      if (current_node === null) return null // reached end of list
      current_index++
    }
    return current_node.data
  }
  insertAtEnd(value) {
    const newNode = new Node(value)
    this.tail.next = newNode
    newNode.previous = this.tail
    this.tail = newNode
  }
  removeFromFront() {
    const removedNode = this.head
    this.head = this.head.next
    this.head.previous = null
    return removedNode
  }
  readAllBackwards() {
    let current_node = this.tail
    while (true) {
      if (!current_node) break
      console.log(current_node.data);
      current_node = current_node.previous
    }
  }
}

let list = new DoublyLinkedList(node1, node4)
list.readAllBackwards()

export { DoublyLinkedList, Node }

```

