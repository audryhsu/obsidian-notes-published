---
creation date: 2022-03-31 08:23
aliases: 
tags: 🖥️
---

###### Description [Queue Code Implementation](Queue%20Code%20Implementation.md)
To implement a queue using JavaScript with O(1) insertion/deletion, we *cannot use `shift`*, since this is O(N) operation.
Options:
- use a doubly linked list
- use an array + hashmap (via [Dmitri Pavlutin](https://dmitripavlutin.com/javascript-queue/))
	- `headIndex` tracks the array index of first in item
	- `tailIndex` tracks index where next item can be enqueued
	- HashMap allows O(1) inserts/deletes
###### Implementation using Array + Hashmap
```js
class Queue {
	constructor() {
		this.headIndex = 0;
		this.tailIndex = 0;
		this.map = {};
	}
	enqueue(value) {
		this.map[tailIndex] = value
		this.tailIndex++;
	}
	dequeue() {
		const value = this.map[this.headIndex]
		delete this.map[this.headIndex]
		this.headIndex++;
		return value
	}
	peek() {
		return this.map[this.headIndex]
	}
	getLength() {
		return this.tailIndex - this.headIndex
	}
}
```

###### Implementation using [Doubly linked list](./Doubly%20Linked%20List%20Code%20Implementation.md)
```js
import { DoublyLinkedList, Node } from './doubly_linkedlist.js'

let list = DoublyLinkedList;

class Queue {
  constructor(dll) {
    this.list = new DoublyLinkedList()
  }

  enqueue(value) {
    this.list.insertAtEnd(value)
  }

  dequeue() {
    this.list.removeFromFront()
  }
  read() {
    if (!this.list.head) return null
    return this.list.head.data
  }
}

```