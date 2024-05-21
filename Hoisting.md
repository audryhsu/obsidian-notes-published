---
creation date: <% tp.file.creation_date() %>
aliases: 
tags: 🖥️
---
# What is hoisting
Javascript engine operatins in two main phases:
1. **creation phase** - find all variable, function, and class *declarations* to **record name and scope**
2. **execution phase** - run code line by line

Because JS did prework in creation phase, it **hoists** when execution phase beginnings.
**Hoisting** is code executes as if declarations where moved to the top of their respective scopes.
- function-scoped declarations are moved to beginning of a function
- block-scoped declarations are moved to block's start
- variables declared with `var` are initialized to `undefined` 
	- why? because `var` likes to be complicated >> [Scope > var vs let](./Scope.md#var%20vs%20let)
- variables declared with `let` and `const` are "unset" and float around in the **Temporal Dead Zone** (TDZ) until initialization

>Hoisting in the creation phase is why Javascript engine gives you different ReferenceError Messages. The engine is aware of the `foo` variable in the TDZ.

```js
console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
let foo;

console.log(baz); // ReferenceError: baz is not defined
```

### Hoisting function expressions
- The variable assigned to a function expression obeys the usual hoisting rules for variable declarations.
	- `var` variable is hoisted and globally visible (?) and initialized to `undefined`
	- this is the same for class expressions

Code:
```js
console.log(hello());

var hello = function () {
  return 'hello world';
};
```
Hoisted representation:
```js
var hello;

console.log(hello()); // raises "TypeError: hello is not a function"

hello = function() {
	return 'hello world'
	}
```

### Hoisting Class Declarations
- only the class name gets hoisted, **not the definition**
- classes live in TDZ until definition code executes

### Hoisting variable and function declarations
- if declaring a variable and function with same name, the function gets hoisted to the top and the variable declaration gets discarded (becomes reassignment) 

# Hoisting best practices
- use `let` and `const` instead of `var` to avoid confusion
	- declare variables as close to their first usage as possible (keep the local block scope tight)
- if using `var`, declare all of the variables at the top of the scope
- declare functions before calling them


---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md)

Reference:
Related: 