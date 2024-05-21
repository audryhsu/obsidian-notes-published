---
creation date: <% tp.file.creation_date() %>
aliases: 
tags: 🖥️
---
# Side Effects
Refresher on side effects:
1. Reassigns any non-local variable (e.g. global variable)
2. mutates the value of any object referenced by a non-local variable (e.g. mutate array or object in outerscope)
3. reads from/writes to data entity (files, network collections) non-local to your program
4. raises an exception
5. calls another function that has side effects not confined to current function

Why do we care? Functions that have unexpected side effects are a major source of bugs. 🐛 Generally, we want side effects to be localized as possible to the calling function. Damaging side effects are ones that affect things outside of our calling function. 

### Examples
Input/Output includes anything that acquires data from or sends data outside of the program:
- Reading from a file on the system's disk
-   Writing to a file on the system's disk
-   ==Reading input from the keyboard==
-   ==Writing to the console==
-   Accessing a database
-   Updating the display on a web page
-   Reading data from a form on a web page
-   Sending data to a remote web site
-   Receiving data from a remote web site
-   Accessing system hardware such as:
    -   The mouse, trackpad, or other pointing devices
    -   The clock (e.g. `new Date()`)
    -   The random number generator
    -   The audio speakers
    -   The camera

Raising exceptions (e.g. not handled by `catch`) are considered side effects, i suppose since you are outputting data to the console

Example of a "local" side effect that can't be seen outside the calling function. aka a we-don't-care-side-effect
```js
function insertNumberInOrder(arrayOfNumbers) {
  arrayOfNumbers = arrayOfNumbers.slice(); // creates a copy of an array
  arrayOfNumbers.push(arrayOfNumbers); // not a side effect since copy of array
  arrayOfNumbers.sort((a, b) => a - b); // sort has **local** side effects
  return arrayOfNumbers; // function has no side effect
}
```
We would not say that `insertNumberInOrder` has side effects, since the array mutations inside the function are localized to the function's copy of `arrayOfNumbers`.

```ad-tip

Whenever possible, functions should return either a useful value or have a side effect. Mixing both increases the risk that you forget to properly handle one or the other.

```


---
Tags: [Programming](Programming.md)

Reference:
Related: 