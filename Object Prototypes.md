---
creation date: 2022-03-14 10:58
aliases: 
tags: 🖥️
---

# [Object Prototypes](Object%20Prototypes.md)
---
an alternative to factory functions to extract code in one place for reuse across multiple objects.
The **prototype** - the object that you "inherit" properties and methods from.

##### Setting & getting prototypes
We can create a new object that has access to all the properties and methods as it's prototype with `Object.create(%3CprototypeObj%3E)`
- `Object.setPrototypeOf(target, prototype)` - set prototype of existing objects

Below, we can also use an Object static method to return an object's prototype, or access the new object's `__proto__` property (*deprecated*). 

```js
let obj = {1: 'hi', 2: 'bye'}
let newobj = Object.create(obj)

newobj.__proto__ // { '1': 'hi', '2': 'bye' }
Object.getPrototypeOf(newobj) // { '1': 'hi', '2': 'bye' }

```
#### But, prototypes aren't classes, right? 
CORRECT! Prototypal inheritence gives the new object *access* to the prototype's properties and methods, however they are *not copied* to the new object. This is because prototypal inheritence (the nomeclature is really using "inheritence" in the loosest sense) really functions more like **delegation**.

Below, newobj is an empty object, with no properties or keys. However, when we try to access the `1` property, we get `hi`. This demonstrates that newobj is delegating to the prototype `obj`. 

```js

newobj.hasOwnProperty(1) // false
Object.keys(newobj) // []
newobj // {}

newobj[1] // 'hi'
```

> Objects hold a **reference** to their prototype objects through the `[[Prototype]]` property. Changes in the prototype are observable in the inherting object.

A quick analogy:
Class-based inheritence is like biological inheritence from a parent. When a child of the parent inherits many of the same traits and behaviors as their parent because the child's genome and DNA is directly made from it's parent. Biological shared traits come directly from the shared DNA from conception.

Prototypal inheritence is like being an adopted child. The child biologically does not share DNA with it's adopted parent, however the child will rely on it's adopted parents for guidance and delegate important decisions and may be shaped behaviourly by his/her adopted parents all the same, but instead through a direct DNA link, it's more like delegation through this relationship rather than a link through biology.

## Prototype Chain
Prototyping (and the subsequent delegation of properties) is like a chain -- if the immeidate object doesn't have it's "own" property, the engine will look up the chain, one link at a time, until it finds the property or it returns `undefined`.  

What if two objects in the prototype chain have a property with the same name? The engine will return the object property that is closer to the calling object.

The very beginning of the prototype chain will always be the default `Object.prototype`. The prototype of `Object.prototype` is `null`, which is why if we loop up the chain when a property doesn't exists, `Object.getPrototypeOf(null)`  returns `undefined`. 

It's methods are available to any JavaScript object:
 -  `Object.prototype.toString()` returns a string representation of the object.
-   `Object.prototype.isPrototypeOf(obj)` determines whether the object is part of another object's prototype chain.
-   `Object.prototype.hasOwnProperty(prop)` determines whether the object contains the property.>)

### The global object
All objects inherit from the `Object` constructor function's prototype..aka `Object.prototype`.
- the prototype of `Object.prototype` is `null`.
- created when JS starts running and serves as **implicit execution context** for function invocations. 
- When you assign a value to a variable without using `let` `const` or `var`, the variable is added as a property on the global object. 
- When accessing a variable for which there is no local/global variable, JS looks at the global object's properties.

206-456-5144
![Pasted image 20220922095937.png](./images/Pasted%20image%2020220922095937.png)

Useful methods on `Object.prototype`:
- -   `Object.prototype.toString()` returns a string representation of the object.
-   `Object.prototype.isPrototypeOf(obj)` determines whether the object is part of another object's prototype chain.
-   `Object.prototype.hasOwnProperty(prop)` 

### Method and property lookup sequence
- JS first looks for an "own" property on the object. If not defined, then looks in obj's prototype, aka the object referenced by internal `[[Prototype]]` property.
- A downstream object in the chain overrides an inherited property if it has a property with the same name. 
- If `Object.prototype` doesn't define the property, then the property access evaluates to `undefined`

use `Object.getOwnPropertyNames(obj)` and `obj.hasOwnProperty(propName)` to test whether an object owns a given property.


###   `Object.assign` and `Object.create`
- `newObj = Object.create(proto, arg..)` - returns a new object that inherits properties from an existing object.
	- How does it inherit? newObj's `[[Prototype]]` is set to the prototype object passed in. 
	- can be used to create new plain objects that inherit/delegate properties to the prototype object.
	- use this to create "subclasses" in pseudo-classical creation pattern
	- `newObject.prototype = Object.create(constructorFunc.prototype)`
---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: 