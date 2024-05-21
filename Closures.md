---
creation date: <% tp.file.creation_date() %>
aliases: 
tags: 🖥️
---
# [Closures](Closures.md)
Closures are good for:
- currying (TB discussed)
- emulating private methods (made possible by [Immediately invoked function expressions (IIFE)](./Immediately%20invoked%20function%20expressions%20(IIFE).md))
- creating functions that can only be executed once
- [Memoization](./Memoization.md) (to avoid repetitive resource-intensive operations)
- iterators and generators
- module pattern (putting code and data into modules)
- asynchronous operations
## What is a closure
Closures and [scope](./Scope.md#Lexical%20scope%20inner%20outer) are intimately related. Closures use lexical scope **at the function's definition point** to determine what variables that function can access.
- closure is created when you **define** a function or method
- the function definition and identifiers in lexical scope are attached to the function in it's closure envelope
- each variable name from the lexical scope of the definition is a pointer to the original variable, NOT the value

Closure "envelope" 💌 stores a *pointer* 👉 to the *variables* available to the function. It is NOT a point-in-time copy. 
- Changes to the value that the variable references are observable. 👁️
Once the function is invoked, it can access any variables it needs from the envelope 📩 **even if those variables aren't in the lexical scope where you *invoke* the function**

So a function can use variables that aren't in scope?

Yes, because when we say something is not "in scope", we mean that it isn't in scope *at the point in the program where the function is invoked*. Closures identify variables in lexical scope of the function *definition*.

Again, ==for closures, where you invoke a function is not important, it's where you **define** the function that creates the closure around the variables it needs to be available to the function later.==

>Closure definitions are purely lexical. **Closures are based on your program's structure**, not by what happens when you execute it. **Even if you never call a particular function, that function forms a closure with its surrounding scope.**

```ad-quote
title: Closures explained in 2 sentences

*The closure is a function that remembers the variables from the place where it is defined, regardless of where it is executed later.*

```
#### Mental model
Under the hood during function invocation:
1. Function execution encounters a variable name. 
2. Function first looks inside local scope for name 🔍
3. If it can't find name, it looks inside closure envelope. 📩 
4. If it's there, JS follows the pointer 👉 to the variable and gets the current value of the variable.
	1. Inside the closure envelope is where JS continues to look to the outer scopes until it gets to the global scope.


A great #resource that steps through lexical scope & closure example in great detail on [Medium](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8) 
## Private data
[Higher Order Functions](./Higher%20Order%20Functions.md) that return functions are the most powerful feature of closure in JavaScript.

Both `fun1` and `fun2` close over the same `counter` variable. We see that they increment the same counter so that it's final value is `3`
In addition, `counter` is private. No other functions outside of `fun1` and `fun2` can access it. 
```js
function makeCounter() {
  let counter = 0;

  const fun1 = function() {
    counter += 1;
    return counter;
  }

  const fun2 = function() {
    counter += 2;
    return counter;
  }

  return [fun1, fun2];
}

let funs = makeCounter();
let fun1 = funs[0];
let fun2 = funs[1];
console.log(fun1()); // 1
console.log(fun2()); // 3
```
###### Example: Private Portfolio
```js
let portfolio = (function createStockPortfolio(initialBalance) {
  let balance = initialBalance;
  let stocks = {};

  return {
    buy(stock, shares, pricePerShare) {
      let totalCost = shares * pricePerShare;
      if (totalCost > balance) {
        throw Error('Insufficient balance to purchase shares');
      }

      if (!(stock in stocks)) {
        stocks[stock] = 0;
      }

      stocks[stock] += shares;
      balance -= totalCost;
    },
    sell(stock, shares, pricePerShare) {
      if (!(stock in stocks)) {
        stocks[stock] = 0;
      }

      if (stocks[stock] < shares) {
        throw Error("You don't own enough shares of that stock.");
      }

      let totalPrice = shares * pricePerShare;
      balance += totalPrice;
      stocks[stock] -= shares;
    },
    getBalance() {
      return balance;
    },
    getStocks() {
      return stocks;
    },
  };
})(10000);

console.log(portfolio.balance); // undefined
console.log(portfolio.getBalance()); // 10000

portfolio.buy('AAPL', 1, 100);
console.log(portfolio.stocks); // undefined
console.log(portfolio.getStocks()); //{ AAPL: 1 }
portfolio.buy('AAPL', 1, 500000); // Error: Insufficient balance to purchase shares

```


- Only the `portfolio` methods can access and manipulate the `balance` and `stocks` properties
-  proper validations to throw errors are used as safeguards
### Partial function application
Another example of closures at work. 
**Partial function application** applies some of the function's arguments when called, and applies the remaining arguments later when you call the returned function. 

```ad-tldr

Partial function application refers to the creation of a function that can call a second function with fewer arguments than the second function expects.

```
It's like half stepping. You know at least one of the two arguments now, so just pass in the known argument and return another function that can take the second argument later on. 

```js
function add(first, second) {
  return first + second;
}

function makeAdder(firstNumber) {
  return function(secondNumber) {
    return add(firstNumber, secondNumber);
  };
}

let addFive = makeAdder(5);
let addTen = makeAdder(10);

console.log(addFive(3));  // 8
console.log(addFive(55)); // 60
console.log(addTen(3));   // 13
console.log(addTen(55));  // 65
```

Key: `makeAdder` must return another function. If it just called `add` instead of returning another function, it's just a regular function call. 
- If we don't return a function, we miss the whole point of creating a "partially applied" function. 

#review [partial function applications](https://launchschool.com/lessons/43f23069/assignments/4d87c212)

# Closures and private data
What to know:
- write code that uses closures to create private data
- explain why private data is desirable
- identify code that gives users of your code a way to alter private data

We can use closures to restrict access to **Private data** to ensure other developers use the intended interface (methods and properties) to update state.

Benefits:
- data integrity (our methods may have more data validation)
- reduce external developers' dependence on implementation to futureproof code (e.g. [APIs](./APIs.md) should be written for users to be able to use methods without relying on data structure details like array methods)

>Private data is not synonymous with secure data. Private data can become exposed easily through references, debugging, or error messages.


---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md)
Reference:
Related: 