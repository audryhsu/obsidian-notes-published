---
creation date: <% tp.file.creation_date() %>
aliases: 
tags: 🖥️
---
# `var` vs `let`
- If used to declar variables in the top level program, `var` creates a property on the global/window object.
	- This does not happen if we run JS program from a file, because Node wraps our code in an invisible function wrapper, thus any `var` declarations become function blocked to the wrapper.  
- `var` is **function scoped** aka it is visible within the function where it is declared

Example of function v block scope
```js
function foo() {
  if (true) {
    var a = 1; // function scoped; available everywhere inside function
    let b = 2; // block scoped
  }

  console.log(a); // 1
  console.log(b); // ReferenceError: b is not defined
}

foo();
```

Side effect of `var` - the `a` variable was still declared and initialized to `undefined` even though the `if` block was NOT run. This is due to [Hoisting](./Hoisting.md#)
```js
function foo() {
  if (false) {
    var a = 1;
  }

  console.log(a); // undefined
}

foo();
```

# Scope talk
LS uses declared scope, visibility scope, and lexical scope as terms to be precise when discussing variable scoping. A variable can have more than one of these types of scopes.

```ad-hint

Lexical scope and visibility scope are distinct. Visibility scope is a high-level way of talking about the scope of a variable (e.g. global vs local). Lexical scope is a lower-level view is purely dependent on structure of code and if it's available at a point in time. 

```


### Declared Scope (block/function)
- **declared scope** refers to how a variable was declared (e.g. `let`, `const`, `class`, `var`, or `function`)
	- `let`, `const`, `class` - *block scoped*
	- `var` & `function` - *function scoped*

>Don't get confused here. All variables, even if not inside a function or block {}, have a ***declared scope, which depends on which keyword was used to declare it***. 
### Visibility Scope (local/global)
- **visibility scope** refers to where a variable is *visible* (but not necessarily available), either **global** or **local** scope (inside a function or block)
- determined as a combination of *how* the variable is declared and on the *lexical location* of each declaration.

```js
let foo1 = 1;        // visibility scope is global
var bar1 = 2;        // visibility scope is global

if (true) {
  let foo2 = 3;      // visibility scope is local (local block)
  var bar2 = 4;      // visibility scope is **global**
}

function xyzzy() {  // visibility scope is global
  let foo3 = 5;     // visibility scope is local (**local function)**
  var bar3 = 6;     // visibility scope is local (local function)

  if (true) {
    let foo4 = 7;   // visibility scope is local (local block)
    var bar4 = 8;   // visibility scope is local (local function)
  }
}
```
### Lexical scope (inner/outer)
- **lexical scope** refers how structure of code determines what variables are accessible or inaccessible at any point
- described in relation to something else (e.g. a function or block)

Less intuitive with the `var` statement:
```js
var bar1 = 1;     // outer scope of xyzzy, outer scope of if block on line 3

if (true) {
  var bar2 = 3;   // outer scope of xyzzy, outer scope of if block on line 3
}

function xyzzy() {
  var bar3 = 5;   // inner scope of xyzzy, outer scope of if block on line 10

  if (true) {
    var bar4 = 7; // inner scope of xyzzy, outer scope of if block on line 10
  }
}
```

Additional discussion from [Dmitiri's blog](https://dmitripavlutin.com/simple-explanation-of-javascript-closures/)

Scope is important because it is intimately tied with [Closures](./Closures.md). 

```ad-important

Lexical scoping means that inside the inner scope, you can access variables of outer scopes. *Lexical* or *static* because engine determines this **at lexing time** before execution.

```

When a function is invoked outside of it's lexical scope (e.g. no where near where it was defined), the closure captures the variables from the invoked function's lexical scope. 

*Simpler, the closure is a function that remembers the variables from the place where it is defined, regardless of where it is executed later.*

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md)

Reference:
Related: 