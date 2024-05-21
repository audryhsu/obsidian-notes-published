---
creation date: 2022-01-25 12:36
aliases: 
tags: 🖥️
---

# [Promises](Promises.md)
---
Promises are *objects* that represent *eventual value*, like a placeholder. The value could be a completion or failure of an asynchronous operation. The asynchronous method returns a _promise_ to supply the value at some point in the future. The promise object serves as a link between the *executor* (producing) code and the consuming functions (logic that uses the result). ^71c8b4

#### Why use promises?
- They are a powerful abstraction that allows for more readable, cleaner code
-  We can write asynchronous code that resembles synchronous code
-  [Chaining](Promises.md#Chaining) allows us to define *multiple callbacks* and ONE error handling block for the promise block instead of one for each callback
- Methods like `Promise.all` can return a single promise from an array of promises, which resolves when all iterable promises are done.
- We can use the  [Async Await](./Async%20Await.md) syntax for gracefully (more so than callbacks) implementing asynchronous, non-blocking code. 

## Creating a Promise 
The `new Promise()` constructor function is used to wrap some function, called executor function. It takes an executor function as an argument that is immediately invoked by the Promise constructor and  returns a  `Promise` object. 

```js
let myPromise = new Promise((resolve, reject) => {
	//async operation, like fetch a url
	if (dataFetched) resolve(data);
	else { reject(error) }

	return 'This is ignored'// we don't need a return value.
})
```

The executor function defined by the developer and expects two callback functions (`resolve` and `reject`; these can be named anything) that are invoked with the corresponding return value after the promise is either resolved or rejected.
- Note: The `return` value of the executor function is ignored. The executor function terminates once the `resolve` or `reject` callbacks are invoked

These two callbacks communicates back to the caller what the resulting promise state was, and what to do with it. 
```js
resolve(value) // call when promise resolved, promise state <fulfilled>
reject(reason) // called on rejected promise, promise state <rejected>
```
- the `value` parameter can be anything, even another promise that can added to the promise chain

## Power of Promises > Callbacks
By **attaching** callback functions to the promise object using the `then` keyword (instead of passing callbacks into a function), promises provide a number of useful guarantees:
- callbacks added with `then` will never be invoked before the completion of the current run of JavaScript [Event Loop](./notes/Event%20Loop.md)
- callbacks will be invoked even if they were added **after** the success/failure of async operation
- you can tack on multiple callbacks by chaining `then`, which will be invoked in order they were inserted

### Promise workflow
![Pasted image 20220125130413.png](./images/Pasted%20image%2020220125130413.png)

##### Promise states
A Promise object has three states:
1. *pending* - initial state that is neither fulfilled nor rejected
2. *fulfilled with a value*- operation was successful
3. *rejected with an error* - operation failed

A promise is referred to as *settled* or *resolved* if it is either fulfilled or rejected (not pending). 

##### Executor Code 
Creating a new promise object by calling the Promise constructor function and passing in an anonymous function that has the expected `resolve` and `reject` callbacks
```js
// Create promise object with executor code
let myPromise = new Promise((resolve, reject) => {
  let finishedTask = true;
  if (finishedTask) {
    resolve('You finished your task!') // promise fulfilled
  } else {
    reject('Did not finish...') // rejected; pass in error object with callback
  }
});
```
- the function passed into `new Promise()` is the *executor* and *runs automatically and immediately*. *Executor*code  will produce the result of the promise.
- The executor will call either the resolve and reject are callbacks (provided by JS) 

##### Consumer code
- The *consumer* code asynchronously does something with the settled promise object
```js
// Consumer code
let handleSuccess = (message) => console.log('Success: ' + message);
let handleReject = (error) => console.log('Failed: ' + error);
let handleFinally = () => console.log('This promise is fulfilled.');

// Consume promise
myPromise
  .then(message => handleSuccess(message)) // ASYNC ACTION **handler does not get called until myPromise is resolved** 
  .catch(error => handleReject(error))
  .finally(handleFinally); // always executed
```
### then, catch, finally
##### **then**
- Once a pending promise is either fulfilled with a value or rejected with an error, the handlers attached to the promise via `then` method are called. 
- `then` takes two arguments: 
	- a function that runs when a promise is resolved, and receives the result of the promise 
	- a function that runs when promise is rejected and receives error (optional)

##### **Catch**
 `.catch` handles errors in promises: a `reject()` call, or an error thrown in a handler.
- returns **a new promise** which resolves to the return value of the callback
- ==control goes to the nearest `catch` statement== down the chain and skips all the remaining chained `then`s .  
- error handler should analyze errors (custom error classes help) and rethrow unknown ones (maybe they are programming mistakes).
- if error is handled and finishes normally (no re-throw error), continues to next closest successful `.then` handler
- You can also chain `catch` statements to handle errors as you catch and throw new ones

```js
// the execution: catch -> catch
new Promise((resolve, reject) => {

  throw new Error("Whoops!");

}).catch(function(error) { // (*)

  if (error instanceof URIError) {
    // handle it
  } else {
    alert("Can't handle such error");

    throw error; // throwing this or another error jumps to the next catch
  }

}).then(function() {
  /* doesn't run here */
}).catch(error => { // (**)

  alert(`The unknown error has occurred: ${error}`);
  // don't return anything => execution goes the normal way

});](<myPromise.then(null, handleReject)
// is equivalent to
myPromise.catch(handleReject)>)
```
- If we're interested in errors, we can use null as the first argument or use `catch`

**Finally**
`.finally(callback)` *always* runs when the promise is settled (resolve or reject, doesn't matter).
-  takes a callback function only, which is called when the promise is settled and **returns a new promise** that is resolved when the original promise is resolved.
- it *does not use* any arguments passed to it from the promise chain. 
	- ==executes independently of anything that happened before it in the chain.== 
-  good for performing clean up that is independent of the result
- use case: when you _do not care_ about the rejection reason, or the fulfillment value, and so there's no need to provide an argument.


## Chaining
Chaining allows seamless execution of 2+ async operations back to back, where the subsequent operation can use the results from the previous one, creating a **promise chain**. 
- Promise methods `promise.then()`, `promise.catch()`, and `promise.finally()` are used to associate further action with a promise that is resolved.

Each invocation of `then` returns a **new promise** after being settled, and takes two args, a callback to handle resolved promise, and a callback to handle a rejected promise.

>💁‍♀️ We can replace promise chaining with the [Async Await](./Async%20Await.md) syntax


> [!Promise Chaining Example]-
> ```js
> const myPromise = new Promise((resolve, reject) => {
>   setTimeout(() => {
>     resolve('foo');
>   }, 300);
> });
> 
> myPromise
>   .then(handleResolvedA, handleRejectedA)
>   .then(handleResolvedB, handleRejectedB)
>   .then(handleResolvedC, handleRejectedC);
> 
> // more commonly only pass one callback to then, and define one catch statement at end
> myPromise
> .then(value => { return value + ' and bar'; })
> .then(value => { return value + ' and bar again'; })
> .then(value => { return value + ' and again'; })
> .then(value => { return value + ' and again'; })
> .then(value => { console.log(value) })
> .catch(err => { console.log(err) });
> 
> ```
> - Unless there is an immediate need to handle a rejection, it is common to throw an error when it occurs and to let one catch statement at the end handle the error instead of defining a `handleRejection` callback for every `then`. 
> - ==The promise status is settled based on the *first occurrence* of resolve or reject. Any additional attempts to settle it will fail silently.==

> [!Example: Multiple settle attempts]-
> 
> 
> ```js
> const promise = new Promise((resolve, reject) => {
>   resolve("Got it!");
>   reject("Oops."); // silent fail - ignored
>   resolve("Again!"); // silent fail - ignored
> });
> 
> promise
>   .then((res) => {
>     console.log(res); // 'Got it!'
>   })
>   .catch((err) => {
>     console.log(err);
>   });
> ```

### Orchestrating Promises
```ad-summary
title: Promise methods

1. *"I need all of my promises to resolve successfully. A rejected promise is not acceptable"* --> `Promise.all`
1. *"I need just the first promise that resolves successfully"* --> `Promise.any`
1. *"I want the first promise that settles. I don't care if it's success or reject"* --> `Promise.race`
1. *"I want all my promises to finish, and I care about the value of each one, even if they reject."* --> `Promise.allSettled`



```

- `Promise.all(iterableOfPromises)` - returns *a single promise* that resolves to an array of results
	- ⛔ if any promises reject, `Promise.all` ==immediately== rejects and won't wait for everything to finish.
	- appropriate to use if tasks are dependent on each other and you need to preserve the order of the resolved values
	- ==returned values will be in the order of the Promises passed regardless of completion order==
- `Promise.any(iterable)` - returns a single pending promise that returns into one value and short-circuits after first promise fulfills
	-  resolves *as soon as any of the promises fulfills* and returns value of first fulfilled promise. 
	- ⛔ if all promises rejected, returned promise is an `AggregateError`
- `Promise.race(iterable)` -  returns a promise that resolves as soon as one of the promises settles and it's corresponding values
	- if a non-promise or already settled promise is present in the iterable, whichever one **comes first** is returned as the resolve value. 
- `Promise.allSettled(iterable)` - returns a single pending promise that will be fulfilled asynchronously after ==*all* given promises have either fulfilled or rejected.== 
	- Promise resolves into an array containing an object with (`status` and `value/reason` keys)  that represent the outcome of each promise. 
		- appropriate if tasks are not dependent on each other and you want to know the results of each promise

> [!INFO] Order of fulfillment
> The order in which you invoke asynchronous functions **does not guarantee which promise will settle first**. They may not even settle consistently in the same order if invoked multiple times.  You can wrap the function calls in another async function and use the await keyword to invoke the nested async functions to ensure resolve order. 

## Common errors

##### Uncaught TypeError: undefined is not a promise
If you get the `Uncaught TypeError: undefined is not a promise` error in the console, make sure you use `new Promise()` instead of just `Promise()`

##### UnhandledPromiseRejectionWarning
This means that a promise you called rejected, but there was no `catch` used to handle the error. Add a `catch` after the offending `then` to handle this properly.



# Examples
###### Order of Execution
```js
const promise1 = new Promise((resolve, reject) => {
  console.log("Callback code runs immediately");
  console.log('Start: Asynchronously fetching data...');

  setTimeout(() => {
    resolve('Success, data!');
  }, 5000);

  console.log("callback code complete");
});

// current state of promise1: Promise { <pending> }

promise1.then((value) => {
  console.log("Done loading data!");
  console.log(value);
}).catch((err) => console.log(err));

console.log("Synchronous code runs before Promise consumer code");

/*
Output:
Callback code runs immediately
Start: Asynchronously fetching data...
callback code complete
Synchronous code runs before Promise consumer code

...after 5 seconds...
Done loading data!
Success, data!
*/
```

###### Comparing concurrent async requests vs sequential async requests
```js
function resolveAfter2Seconds() {
  console.log("starting slow promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve("slow");
      console.log("slow promise is done");
    }, 2000);
  });
}

function resolveAfter1Second() {
  console.log("starting fast promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve("fast");
      console.log("fast promise is done");
    }, 1000);
  });
}

async function sequentialStart() {
  console.log('==SEQUENTIAL START==');

  // 1. Execution gets here almost instantly
  const slow = await resolveAfter2Seconds();
  console.log(slow); // 2. this runs 2 seconds after 1.

  const fast = await resolveAfter1Second();
  console.log(fast); // 3. this runs 3 seconds after 1.
}

async function concurrentStart() {
  console.log('==CONCURRENT START with await==');
  const slow = resolveAfter2Seconds(); // starts timer immediately
  const fast = resolveAfter1Second(); // starts timer immediately

  // 1. Execution gets here almost instantly
  console.log(await slow); // 2. this runs 2 seconds after 1.
  console.log(await fast); // 3. this runs 2 seconds after 1., immediately after 2., since fast is already resolved
}

function concurrentPromise() {
  console.log('==CONCURRENT START with Promise.all==');
  return Promise.all([resolveAfter2Seconds(), resolveAfter1Second()]).then((messages) => {
    console.log(messages[0]); // slow
    console.log(messages[1]); // fast
  });
}

async function parallel() {
  console.log('==PARALLEL with await Promise.all==');

  // Start 2 "jobs" in parallel and wait for both of them to complete
  await Promise.all([
    (async() => console.log(await resolveAfter2Seconds()))(),
    (async() => console.log(await resolveAfter1Second()))()
  ]);
}

sequentialStart(); // after 2 seconds, logs "slow", then after 1 more second, "fast"
// wait above to finish
setTimeout(concurrentStart, 4000); // after 2 seconds, logs "slow" and then "fast"
// wait again
setTimeout(concurrentPromise, 7000); // same as concurrentStart
// wait again
setTimeout(parallel, 10000); // truly parallel: after 1 second, logs "fast", then after 1 more second, "slow"

```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) - [Asynchronous Programming](./Asynchronous%20Programming.md)

Reference: https://javascript.info/promise-basics - [Using Promises]( https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

Related: [Async Await](./Async%20Await.md) 

