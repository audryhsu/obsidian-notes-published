---
creation date: 2022-03-14 10:56
aliases: 
tags: 🖥️
---

# [Objects](Objects.md)
---
## Object Properties
an object can be any of the 9 data types in JS
-  String
-   Number
-   Boolean
-   Null
-   Undefined
-   Object
-   BigInt (you don't need to know about this)
-   Symbol (you don't need to know about this)

Object properties are **always strings**. We can use dot notation (aka member access notation) or bracket notation (e.g. computer member access notation).

#### Checking for property existence
if a property does not exist, `undefined` is returned. To confirm if a value is explictly set to `undefined` vs a missing key, use:
- `myObject.hasOwnProperty(prop)` or `prop in myObject` -  return boolean if that property exists. 

We can enumeratie properties of an object in two ways:

1. `Object.getOwnPropertyNames(myObject)` - returns array of *all* "own" properties, including non-enumerable properties (e.g. non-iterable properties and methods)
3.  `for/in` - iterates over enumberable properties of object **and it's prototype**
4.    `Object.keys(myObject)`- returns an array of *enumerable* "own" properties


### Method invocation vs. function invocation
#### function syntaxes
syntactical and behavioral differences between 
- function declarations
	- get **hoisted**, an internal step performed by the engine to "move" function decalrations to the top of the program file or the top of the function in which tehy are nested. This allows us to invoke a function before it's physically defined in file. 
- function expressions - function definitions that are part of an expression.
	- saving a function to a variable or any function definition without `function` keyword at the beginning.
	- JS functions are **first class functions** aka can be passed around like any other value or return to another function. Why? because all functions are **objects**
	- will not be hoisted.
	- can be named or anonymous

Also a function expression:
```js
(function greetPeople() { // This is a function expression, not a declaration
  console.log("Good Morning!");
});
```
-  arrow functions: similar to function expressions, just replaces the `function` keyword and optionally {} brackets and return statement if it's a single expression
-  compact method syntax used in classes and objects.
```js
newObj = {
myMethod: function() {
	// implementation
	},
	myMethod() {
	// implementation
	}
},
```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: 