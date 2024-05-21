---
creation date: 2022-04-07 11:32
aliases: 
tags: 🖥️
---

# [State in React and Redux](State%20in%20React%20and%20Redux.md)
---
#tolearn  [React Hooks vs Redux](https://www.simplethread.com/cant-replace-redux-with-hooks/)

One of the biggest concepts of managing state in [React](./React.md) and [Redux](./Redux.md) is that state objects should never be mutated, but copied and replaced. 

🔑 **New objects represent new state.**

They accomplish this by:
1. Only allowing one specific avenue for updating state (*reducers* in Redux and [|useState hook in React components](./React.md#useState%20hook)), and
2. Require state changing functions *to be pure functions.*
 
### Why pure functions are required
##### **Detecting changes in state**
To determine if state has changed and a re-render is needed, React and Redux check if the state object is a different object by comparing the *memory addresses*. If we mutate the state object, the memory address stays the same and React/Redux will not register the change in state. 

React/Redux were designed this way because *comparing memory addresses are much more efficient than checking for deep equality in JavaScript objects*, which is the only way to know if two objects have the same properties.  Deep equality means traversing the *entire* object tree!

##### **Time travel and debugging**
Every past version of state object can stored like a "snapshot". The “time travel” feature that allows us to review app history and “jump back” to previous moves (an ability to undo and redo certain actions), which is a common requirement.
- Avoiding direct data mutation lets us keep previous versions of the game’s history intact, and reuse them later.

The tradeoff is favorable, as this is not too big of an ask of developers since we can use the object and array [spread](./JavaScript%20ES6%20Features.md#spread%20syntax) operators to copy arrays and objects. 


---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference:

Related: [React](./React.md) - [Redux](./Redux.md)