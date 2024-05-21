---
creation date: 2022-04-17 21:41
aliases: 
tags: 🖥️
---

###### Description [Dijkstra Algorithm](Dijkstra%20Algorithm.md)
Shortest path for a graph of flight routes from [A Common Sense Guide to Data Structures and Algorithms](./A%20Common%20Sense%20Guide%20to%20Data%20Structures%20and%20Algorithms.md)

![Pasted image 20220417214358.png](./images/Pasted%20image%2020220417214358.png)

🌹We use two hash tables to keep track of:
- cheapest *known* price from starting city to all other *known*destinations
	- Identifies the *closest end vertex* (e.g. cheapest destination)
- cheapest *previous* stopover city for a given destination city. 
	- This represents the vertex/city that needs to be visited *immediately* before the given city to achieve the shortest path. 
	- This table is what gives us the data to find the *path*

**Algorithm**
1. Visit the starting city as the "current city"
2. Check prices from current city to each of the adjacent cities
3. If price from current to adjacent city is cheaper than price in `cheapest_prices` table or doesn't exist yet:
	1. add to `cheapest_prices`hash table (`{city: price}`)
	2. update the `cheapest_previous_stopover` table  `{ 'El Paso': 'Denver' }`
		1. Note: this means to get to Denver, I should go through El Paso. 
4. Visit next *unvisited* city that has *cheapest price* from starting city. Set as "current city".
5. Repeat steps 2 - 4 until all known cities have been visited.
6. Using the two hash tables, find the vertex that will give us the shortest path *and* build an array that tells us the sequential order of vertices to visit, from starting vertex to destination vertex 

**Assessing results**
📍 To find the *cheapest city* to fly to from Atlanta, look for the city that has the lowest price in the `cheapest_prices` table.

🗺️ To find the *cheapest path* to get there, look up that city in the `cheapest_previous_stopover` table. This works *backwards* from destination vertex to starting vertex
- 🌇This will return the city you must visit *immediately* before the target city. 
- Keep repeating until the returned city from `cheapest_previous_stopover` is your starting city!

###### Implementation
```js
// shortest path algorithm with a weighted graph
const graph = {
  'atlanta': {'boston': 100, 'denver': 160},
  'boston': {'denver': 180, 'chicago': 120},
  'chicago': {'elpaso': 80},
  'denver': {'chicago': 40, 'elpaso': 140},
  'elpaso': { 'boston': 100}
};

const dijkstra = (graph, start, destination) => {
  // track costs to travel from Start to a given city
  const cheapestCostTo = {}
  // tracks the immediate vertex before a given vertex that represents the cheapest route
  const cheapestLayoverFor = {} 
  
  const visited = {} // track fully visited cities
  const unvisited = [] // track discovered adjacent cities, usually priority queue

  let currentCity = start
  cheapestCostTo[currentCity] = 0

  while (true) {
    visited[currentCity] = true

    // visited city from unvisited
    let index = unvisited.indexOf(currentCity)
    unvisited.splice(index, 1)

    let adjacentCities = Object.entries(graph[currentCity])

    //loop through adjacent cities
    for (const [city, price] of adjacentCities) {
      if (!visited[city]) unvisited.push(city)

      // cost from start city to adjacent city THRU current city
      let priceThroughCity = cheapestCostTo[currentCity] + price

      if (!cheapestCostTo[city] || priceThroughCity < cheapestCostTo[city]) {
        cheapestCostTo[city] = priceThroughCity
        cheapestLayoverFor[city] = currentCity // shortest path to adjacent city is through currentCity
      }

    }
    if (unvisited.length === 0) break // stop when no more unvisited

    // visit adjacent city that is cheapest to get to from STARTING city
    let cheapestAdjacentCity = unvisited.reduce((cheapest, current) => {
      return cheapestCostTo[current] < cheapestCostTo[cheapest] ? current : cheapest
    })

    currentCity = cheapestAdjacentCity
  }

  // calculate shortest path by working backwards from destination
  const shortestPath = []
  currentCity = destination

  while (currentCity !== start) {
    shortestPath.push(currentCity)
    currentCity = cheapestLayoverFor[currentCity]
  }

  return shortestPath.reverse()

}
let path = dijkstra(graph, 'atlanta', 'elpaso') // ['atlanta', 'denver', 'chicago', 'elpaso']

```

