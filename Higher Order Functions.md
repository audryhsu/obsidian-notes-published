---
creation date: 2022-03-15 11:49
aliases: 
tags: 🖥️
---

# [Higher Order Functions](Higher%20Order%20Functions.md)
---
Key feature of first class functions is that you can treat them like any other value. Functions in JS are objects!
1.  Can be assigned to a variable or an element of a data structure
2.  Can be passed as an argument to a function
3.  Can be returned from a function
	1.  In [JavaScript](./JavaScript.md), functions meet this criteria therefore they are first class functions. 
	2.  Higher order functions can take other functions as arguments.
	3.  Array methods such as `map` and `forEach` are higher order


A function that can do at least one of the following is considered higher order
- take a function as an argument (e.g. `map, forEach`)
	AND/OR 
- return a function (e.g. a function factory)
#### Functions that return functions
You can think of a function that returns another function as a "function factory". To take advantage of this feature, we typically pass arguments to the function factory to determine the specific job performed by the function it creates.

Think: [partial function application](./Closures.md#Partial%20function%20application) and [Closures](./Closures.md)! 

Example:
```js
function createGreeter(language) {
  switch (language) {
    case 'en':
      return () => console.log('Hello!');
    case 'es':
      return () => console.log('Hola!');
  }
}

let greeterEs = createGreeter('es');
greeterEs(); // logs 'Hola!' // can be resued over and over again

let greeterEn = createGreeter('en');
greeterEn(); // logs 'Hello!'
```



- `Array.prototype.forEach` (and several other `Array.prototype` methods) take a `thisArg` argument that lets you set the context for the callback explicitly
 -  why do we need to do this? because the callback functions and arrow functions used inside of higher order functions are anonymous functions 



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: 