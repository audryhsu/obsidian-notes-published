---
creation date: <% tp.file.creation_date() %>
aliases: 
tags: 🖥️
---
# what is it
**IIFEs** are functions that you define and invoke simultaneously using IIFE syntax:

Using parenthesis:
```js
(function(number) {
  return number + 1;
})(2);                    // 3
```
Assigning to a variable:
```js
let foo = (function() {
  return function() {
    return 10;
  }() + 5;
})();

console.log(foo);       // => 15
```

Arrow function:
```js
((first, second) => first * second)(5, 6); // => 30
```
- parentheses around function definition indicate it's a function expression, then the `()` at the end invoke the expression immediately

# why use IIFEs
1. Use IIFEs (or blocks in ES6) to create a private scope:
	- mitigates risk of declaring and overwriting variables or function names with the same name in the global/outer scope by isolating to a local function scope 
	- useful when we need an adhoc function in the middle of a big code base (thousands of lines)
2. Use IIFEs to create functions with private data (via [Closures](./Closures.md)), aka encapsulating local data that we want to restrict access to from outer scope.
	
```ad-tip
title: Keep data private to all of a constructor
Use IIFEs to wrap global data relevant only for a constructor and it's prototype methods to hide it from public view.


```
Note: This can also be achieved by putting the constructor & methods in a module and importing it into main.js

Example of private data without IIFE:
```js
function makeUniqueIdGenerator() {
  let count = 0;
  return function() {
    count += 1;
    return count;
  }
};

let makeUniqueId = makeUniqueIdGenerator();
makeUniqueId(); // => 1
makeUniqueId(); // => 2
makeUniqueId(); // => 3
```
Example of private data with IIFE:
```js
const makeUniqueId = (function() {
  let count = 0;
  return function() {
    ++count;
    return count;
  };
})();

makeUniqueId(); // => 1
makeUniqueId(); // => 2
makeUniqueId(); // => 3
```

Breakdown:
- define a function that creates and returns a function that relies on closure to keep `count` private
- assign function to the `makeUniqueId` variable
- invoke `makeUniqueId` whenever you need a new id 

Benefits:
- don't need to declare makeUniqueIdGenerator
- don't need the extra line to invoke the function
---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md)

Reference:
Related: 