---
creation date: 2022-03-01 09:55
aliases: 
tags: 🖥️
---

###### [DOM Manipulation Snippets](DOM%20Manipulation%20Snippets.md)

##### Nodes to Array Recursion
```js
// recursively build nested array of ["PARENT", [Children]]
function nodesToArr() {
  let nodes = [document.body];
  return getGeneration(nodes);

  function getGeneration(parents) {
    return parents.map(parent => {
      let kids = Array.from(parent.children);

      if (kids.length === 0) return [parent.nodeName, []];

      return [parent.nodeName, [getGeneration(kids)]];
    });
  }
}
```
##### Nodes to Array Recursion, alternative
```js
// recursively build nested array of ["PARENT", [Children]]
function nodesToArr() {
  return getTagChild(document.body);

  function getTagChild(node) {
    return [ node.nodeName, [...node.children].map(child => getTagChild(child))];
  }
}
```
##### Color Generation Recursion
```js
// recursively get children of parent elements into one array 
function getChildren(parents) {
  return parents.reduce((allChildren, parent) => allChildren.concat(Array.from(parent.children)), []);
}

function colorGeneration(n) {
  let parents = [document.body];
  let generation;
  let counter = 0;

  if (n < 1) return null;

  while (counter < n) {
    generation = getChildren(parents);
    parents = generation;
    counter++;
  }
  generation.forEach(el => el.classList.add('generation-color'));
}
document.addEventListener('DOMContentLoaded', (e) => {
  colorGeneration(7);
});
```

##### Swap nodes
```js
function nodeSwap(idA, idB) {
  let [a, b] = [document.getElementById(idA), document.getElementById(idB)];

  if (!a || !b) return undefined;
  if (a.contains(b) || b.contains(a)) return undefined;

  let copyA = a.cloneNode(true);
  let copyB = b.cloneNode(true);

  a.parentElement.replaceChild(copyB, a);
  b.parentElement.replaceChild(copyA, b);

  a.remove();
  b.remove();
  return true;
}
```

###### Walk DOM forEach
**Recursive `forEach` for Nodes
```js
// walk() calls the function "callback" once for each node
function walk(node, callback) {
  callback(node);                                                   
// do something with node
  for (let index = 0; index < node.childNodes.length; index += 1) { 
    walk(node.childNodes[index], callback);   // recursively call walk()
  }
}
walk(document.body, node => {                                
  console.log(node.nodeName); // log nodeName of every node
});
```

^7e9846

###### Walk DOM and count nodes
```js
   function walk(node, callback) {
      callback(node)
      let children = node.childNodes

      for (let i = 0; i < children.length; i++) {
        walk(children[i], callback)
      }
    }

let counter = 0;
walk(document.body, (node) => {
  counter++
  console.log(`${node.nodeName}`)
})
console.log(counter)
```

###### Walk and getElementsByTagName
Our homegrown implementation of `getElementsByTagName` under the hood uses the recursive `walk` function to generate an array of HTML elements. Note that the actual built-in methods don't return arrays.

```js
function getElementsByTagName(tagName) {
	let result = []
	walk(document.body)
	
	return result;
	
	function walk(node) {
	  if (node.nodeName === tagName) result.push(node)
	
	  for (let i = 0; i < node.children.length; i++) {
		walk(node.children[i])
	  }
	}
}
```


---
Tags: 

Reference:

Related: 