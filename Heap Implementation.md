---
creation date: 2022-04-14 18:50
aliases: 
tags: 🖥️
---

###### Description [Heap Implementation](Heap%20Implementation.md)

###### Implementation
```js 
// heap as an array 
class Heap {
  constructor() {
    this.data = []
  }
  root() {
    return this.data[0]
  }
  last() {
    return this.data[this.data.length - 1]
  }
  leftChildIndex(index) {
    return (index*2) + 1
  }
  rightChildIndex(index) {
    return (index*2) + 2
  }
  parentIndex(index) {
    return Math.floor((index - 1) /2)
  }
  insert(value) {
    this.data.push(value)
    let newNodeIndex = this.data.length - 1
    let parentIndex = this.parentIndex(newNodeIndex)

    // swap node with parent if node is greater than parent 
    while (newNodeIndex > 0 && value > this.data[parentIndex]) {
      [this.data[newNodeIndex], this.data[parentIndex]] = [this.data[parentIndex], this.data[newNodeIndex]]
      newNodeIndex = parentIndex
      parentIndex = this.parentIndex(newNodeIndex)
    }
    return newNodeIndex
  }

  delete() {
    this.data[0] = this.data.pop() // always delete from root
    let nodeValue = this.data[0]
    let nodeIndex = 0

    // keep trickling down until node is greater than it's descendants
    while (this.hasGreaterChild(nodeIndex)) {
      let largerChildIndex = this.calculateLargerChildIndex(nodeIndex)

      // swap nodes
      ;[this.data[nodeIndex], this.data[largerChildIndex]] = [this.data[largerChildIndex], this.data[nodeIndex]]
      nodeIndex = largerChildIndex
    }

    return nodeIndex

  }
  calculateLargerChildIndex(index) {
    const [leftIndex, rightIndex] = [this.leftChildIndex(index), this.rightChildIndex(index)]
    const leftChild = this.data[leftIndex]
    const rightChild = this.data[rightIndex]

    if (!rightChild) return rightIndex
    if (!leftChild) return leftIndex
    return leftChild > rightChild ? leftIndex : rightIndex
  }
  
  hasGreaterChild(index) {
    const [leftChild, rightChild] = [this.data[this.leftChildIndex(index)], this.data[this.rightChildIndex(index)]];
    return (
      (leftChild && leftChild > this.data[index])
      || (rightChild && rightChild > this.data[index])
    )
  }
}


let heap = new Heap()


```

