---
creation date: <% tp.file.creation_date() %>
aliases: 
tags: 🖥️
---
# ES6 Features
You can use babel transpiler 



#### concise property initialization
```js

```
Use the name of the property we want to initialize and JS looks for a variable with the same name to use as the initial value 
```js
function xyzzy(foo, bar, qux) {
  return {
    foo,
    bar,
    qux,
  };
}
```

#### object destructuring
create and assign variables with the same value as an object's corresponding properties with the same name
```js
let obj = {
  foo: "foo",
  bar: "bar",
  qux: 42,
};

let foo = obj.foo;
let bar = obj.bar;
let qux = obj.qux;
```
```js
let { foo, bar, qux } = obj;
```
The result from this code is identical: `qux` is assigned `obj.qux`, `foo` is assigned `obj.foo`, and `bar` is assigned `obj.bar`

We can also pass in obj destructuring syntax into a function as arguments:
```js
function xyzzy({ foo, bar, qux }) {
  console.log(qux); // 3
  console.log(bar); // 2
  console.log(foo); // 1
}

let obj = {
  foo: 1,
  bar: 2,
  qux: 3,
};

xyzzy(obj);
```

#### array destructuring
works similar to obj destructuring to declare new variables

```js
let bar = [1, 2, 3, 4, 5, 6, 7];
let [ first, , , fourth, fifth, , seventh ] = bar;
```

We can use array destructuring (left side) to shortcut assigning multiple variables via array literal syntax (right side)
```js
let one = 1;
let two = 2;
let three = 3;

let [ num1, num2, num3 ] =  [one, two, three]; // multi-assignment

console.log(num1);   // 1
console.log(num2);   // 2
console.log(num3);   // 3
```

Swapping two values
```js
let one = 1;
let two = 2;

[ one, two ] =  [two, one];

console.log(one);   // 2
console.log(two);   // 1
```

Rest syntax (assign a variable to a range of array values)

```js
let foo = [1, 2, 3, 4];
let [ bar, ...qux ] = foo;
console.log(bar);   // 1
console.log(qux);   // [2, 3, 4]
```
we assign the "rest" of the array to `qux`

#### spread syntax 
spreads the elements of an array or object into separate items where an array literal is expecting one or more elements
useful to:
- copy an array
- concatenate arrays
- insert array into another 
- pass in "spread" array into a function as arguments (func only takes expected n number of args)
```js
function sum(x, y, z,) {
  return x + y + z;
}

const numbers = [1, 2, 3, 4, 5, 6];

console.log(sum(...numbers)); // only expects 3 args, ignores [4, 5, 6]
// expected output: 6
```

Or object expressions when object is expecting one or more key/value pairs


```js
// Merge objects
let foo = { qux: 1, baz: 2 };
let xyz = { baz: 3, sup: 4 };
let obj = { ...foo, ...xyz };
obj;  // => { qux: 1, baz: 3, sup: 4 }
```
>Spread syntax only returns enumerable "own" properties. Not a good choice when attempting to copy objects with inherited properties.

#### Rest syntax
is the opposite of spread syntax; it collects multiple items into an array or object.

Declaring multiple variables via spreading objects:
```js
let foo = {bar: 1, qux: 2, baz: 3, xyz: 4};
let { bar, baz, ...otherStuff } = foo;
console.log(bar);        // 1
console.log(baz);        // 3
console.log(otherStuff); // {qux: 2, xyz: 4}
```

Spread syntax as function argument. Useful when a function can take any number of arguments
```js
function maxItem(first, ...moreArgs) {
  let maximum = first;
  moreArgs.forEach(value => {
    if (value > maximum) {
      maximum = value;
    }
  });

  return maximum;
}

console.log(maxItem(2, 6, 10, 4, -3));
```


---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md)
Reference:
Related: 

