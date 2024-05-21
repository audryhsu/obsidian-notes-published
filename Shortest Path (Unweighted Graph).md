---
creation date: 2022-04-17 21:47
aliases: 
tags: 🖥️
---

###### Description [Shortest Path (Unweighted Graph)](Shortest%20Path%20(Unweighted%20Graph).md)
Use a combination of breadth first search and [Dijkstra Algorithm](./Dijkstra%20Algorithm.md).

We only need one hash table to keep track of adjacent vertex and the current vertex (like the classic algorithm, we use this hash table to work backwards from target vertex to the start vertex to build the "path").
###### Implementation
[Code 📁](file:///home/ahsu/projects/dsa/graph_dijkstra_unweighted.js)

![Pasted image 20220417214854.png](./images/Pasted%20image%2020220417214854.png)
```js
// calculates the shortest path (least number of vertices travelled) in an unweighted graph
import { Vertex } from './graph_vertex.js'
import Queue from './queue.js'

// graph representing a social network
const network = ['idris', 'kamil', 'lina', 'sasha', 'talia', 'ken', 'marco'].map(name => new Vertex(name))
const [idris,  kamil ,  lina ,  sasha ,  talia ,  ken ,  marco ] = network

idris.addAdjacent(kamil)
idris.addAdjacent(talia)
talia.addAdjacent(ken)
ken.addAdjacent(marco)
marco.addAdjacent(sasha)
kamil.addAdjacent(lina)
lina.addAdjacent(sasha)

const shortestPath = (startVertex, targetVertex) => {
  const searchValue = targetVertex.value
  const visited = {}
  const shortestPathTo = {}
  visited[startVertex.value] = true

  let queue = new Queue()
  queue.enqueue(startVertex)
  let currentVertex;

  while (!queue.isEmpty()) {
    currentVertex = queue.dequeue()
    
    for (const neighbor of currentVertex.adjacentVertices) {
      shortestPathTo[neighbor.value] = currentVertex.value

      if (neighbor.value === searchValue) break
      if (visited[neighbor.value]) continue
      visited[neighbor.value] = true
      queue.enqueue(neighbor)
    }
  }
  console.log(shortestPathTo)
  const path = []
  let currentValue = searchValue
  while (currentValue !== startVertex.value) {
    path.push(currentValue)
    currentValue = shortestPathTo[currentValue]
  }
  path.push(startVertex.value)

  return path.reverse()
}
console.log(
shortestPath(idris, lina))
```

