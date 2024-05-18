---
creation date: 2022-02-16 16:07
aliases: 
tags: 🖥️
---

# [Event Loop](Event%20Loop.md)
---
Runtime environments encompass the JavaScript engine (your code), Web APIs (like the DOM, `setTimeOut`, fetching data), the task/message queue, and the event loop. 

The event loop's job is to continuously looks to check if the call stack is empty, and if it is, pushes any message/tasks (e.g. asynchronous callback functions or DOM events) queued in the task queue and pushes it onto the stack. 

### Step by Step
1. When a function is invoked, it and it's execution context (closure) are placed on the top of the call stack, which operates in LIFO manner
2. When an asynchronous function is called, it's placed on top of the call stack. 
	1. The pending callback function or event handler is processed by the Web API outside of the JavaScript runtime.
3. The async function call is complete after starting the async task in the Web API land or registering an event listener, and it is popped off the call stack
4. The call stack can move onto the next frame of synchronous code
5. When the Web API is done processing something, or an event listener is fired, the callback is placed in the task queue.
6. When the call stack is empty after all synchronous code is done, the event loop pushes the callback onto the call stack, and the callback is invoked. 
	1. In the case of an event handler, it sits in the Web APIs environment until the browser fires an event, then the callback function is placed in the message queue

![Pasted image 20220216165748.png](./images/Pasted%20image%2020220216165748.png)
>The event loop, web APIs (or C++ APIs in Nodejs), and message/task queue are part of the browser's JavaScript runtime environment or Nodejs JavaScript runtime.

# Visualizing the Event Loop

**Using asynchronous functions within a loop will produce unexpected results if you don't understand how the event loop works!**
```js
function delayLog() {
  for (let i = 1; i <= 4; i++) {
    console.log('Incrementing i!');

    setTimeout(() => {
      console.log(`delay after ${i} seconds.`);
    }, i * 1000);
  }
}
delayLog();
//Incrementing i!
//Incrementing i!
//Incrementing i!
//Incrementing i!

//delay after 1 seconds.
//delay after 2 seconds.
//delay after 3 seconds.
//delay after 4 seconds.
```
Explanation: 
1 delayLog is placed on the call stack
2 The value of i is checked against `i <= 4`
3 setTimeout is placed on the call stack
4 The anonymous callback passed to setTimeout is pushed to the Web API for one second, then the callback is pushed to the callback queue and waits until delayLog is popped off the stack. 
5 setTimeout is popped off stack
6 `i` is incremented by 1
7 Steps 2 through 6 are repeated three more times
8 delayLog is popped off the call stack
9 The event loop pushes first queued anonymous callback function onto the stack
	10. `console.log(i)` is pushed onto the stack. Prints the value of `i` based on the callabck's execution context  
	11. `console.log(i)` is popped off the stack
	12.  anonymous callback function popped off stack
11 Steps 9 and 12 repeat until the callback queue is empty

**Challenge: ** What if we change `i` declaration to `var`?
```js
function delayLog() {
  for (var i = 1; i <= 5; i++) {
    setTimeout(function() {
      console.log(i);
    }, 1000);
  }
} 

delayLog();
// 5
// 5
// 5
// 5
// 5
```
Explanation: 
- The event loop works the same as above, but since `var` is function scoped instead of block scoped, each callback to the anonymous function has access to the same closure of `i` which, by the time the event loop gets to the `console.log(i)` statements, has already finished incrementing to 5. 

---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: [Understanding Event Loop - Medium](https://blog.bitsrc.io/understanding-asynchronous-javascript-the-event-loop-74cd408419ff) || [Loupe](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gZGVsYXlMb2coKSB7CiAgZm9yIChsZXQgaSA9IDE7IGkgPD0gNTsgaSsrKSB7CiAgICBzZXRUaW1lb3V0KGZ1bmN0aW9uKCkgewogICAgICBjb25zb2xlLmxvZyhpKTsKICAgIH0sIDEwMDApOwogIH0KfSAKCmRlbGF5TG9nKCk7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

Related: 