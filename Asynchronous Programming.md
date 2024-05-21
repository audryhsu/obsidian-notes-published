---
creation date: 2021-12-07 17:24
aliases: 
tags: 🖥️ 🌱
---

# [Asynchronous Programming](Asynchronous%20Programming.md)
---
[Asynchronous Programming](Asynchronous%20Programming.md) allow functions to execute in non-linear fashion. The interpreter will continue to evaluate the rest of the code until the asynchronous operation resolves. This is especially useful when fetching data from a database so you can fetch data while allowing the rest of the code to run. Non-blocking calls are made possible by callbacks.

Asynchronous functions accept a callback, so that the fallback is only executed once the initial operation (like getting data) is complete. 
- If the order of async functions becomes more complex (approaching callback hell), then look to using [Promises](./Promises.md) and the async/await syntax for more intuitive way to write code. 
- You can't tell what functions are asynchronous just by looking at it; you must understand the behavior of the function and know whether the code was invoked asynchronously. 

**Benefits:**
- don't block execution for the rest of the program while they execute, they run concurrently with other operations so you don't have to wait for the task to finish running. 

**Basic asynchronous functions**
`setTimeout(callback, secondsDelay)` is not part of JavaScript spec, most environments and browsers make it available. It's a simple example of async code, as it utilizes the **[Event Loop](./notes/Event%20Loop.md)** and **task queue**. 
- After time expires, setTimeout invokes the callback function
- code being run by `setTimeout` only runs when the engine isn't doing anything else (the callback is processed and return value is queued for the stack in the task queue)
- `clearTimeout(id)` to cancel a timeout previously established
`setInterval(callback, delayInterval)` - returns an identifier. Repeatedly calls a function on a given interval. Clear the interval by passing the identifier to `clearInterval(id)`.


## Iterator methods
The problem with `forEach` and asynchronous callbacks also occurs with other built-in iteration functions, such as `map`, `select`, `reduce`, and others. While it is possible to use asynchronous callbacks in these functions, we're going to avoid that discussion. Feel free to research it on your own. #⭕


# Examples
**setInterval**
```js
function startcounting() {
  let i = 1;
  id = setInterval(() => {
    console.log(i);
    i++;
  }, 1000);
}

let id;
startcounting();
clearInterval(id);
```


Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) - [Promises](./Promises.md)

Reference: [What the heck is the event loop anyways?](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

Related:  [Event Loop](./notes/Event%20Loop.md)