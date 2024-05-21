---
creation date: 2022-01-25 12:05
aliases: 
tags: 🖥️
---

# [Async Await](Async%20Await.md)
---

The **async/await** syntax, which is [JavaScript ES6 Features](./JavaScript%20ES6%20Features.md) on top of [Promises](./Promises.md). Async functions can contain zero or more await expressions. Await expressions make promise-returning functions ==behave as though they're synchronous== ==by suspending execution until the returned promise is fulfilled or rejected.==

```ad-hint

The `await` keyword essentially replaces the need for chaining `then` statements. Instead of using `then` to pass a Promise object between different callbacks, the await keyword returns the value of the resolved promise. 

```
### Syntax
`async` keyword is prepended to a function which allows the `await` keyword to be used for an asynchronous call within the function.
- can contain zero or more await expressions
- ***always return a promise***, even if it's not explicitly wrapped in promise, it will implicitly be wrapped in [`Promise.resolve`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve) 

`await` is used to wait for a `Promise` and can only be used inside an `async` function in regular JavaScript code.
- Returns the fulfilled value of the promise 
- ==pauses function execution== inside an `async` function until the promise is settled. 
	- After each `await` statement, ==control returns to the caller of the async function== 
	- Any code after await inside the `async` function is executed AFTER the promise is settled.
	
Multiple await expressions inside of an async function build the promise chain successively in stages as control is yielded from and returned to the async function. 

### Workflow
1. Define Promisified executor code - a function that that returns a promise object; 
	- Ex: XHR requests and it's accompanying load event listener that resolves into `request.response`
2. Define async function - any code that wants to work with the return value of a promise
	- replaces the need for `then` chaining
	- body of function: 
		- invoke the promisified executor function
		-  *await* the return value from the invocation of the promise executor code

# Examples
```js
loadData('https://api.github.com/repos/rails/rails');
// executor code - XHR request
function getRequest(url) {
  return new Promise(function(resolve, reject) {
    let request = new XMLHttpRequest();
    request.open('GET', url);
    request.responseType = 'json';
    request.send();

    request.addEventListener('load', (e) => {
      alert('Done getting request!');
      resolve(request.response);
    });
  });
}

// consumer code
async function loadData(url) {
  try {
    let response = await getRequest(url);
    alert(`There are ${response.open_issues} open issues. `);
  } catch (e) {
    console.log("Error: ", e);
  }
}
```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) - [Asynchronous Programming](./Asynchronous%20Programming.md)

Reference:

Related:  [AJAX](./AJAX.md)

