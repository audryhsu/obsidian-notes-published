# [Constructor Functions](Constructor%20Functions.md)

`new Rabbit()` is a constructor function
`Rabbit.prototype` is a property on `Rabbit`, which points to an object. This object is used by the `new` operator to set `[[Prototype]]`  for the newly created object.

```js
let animal = {
  eats: true
};

function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype = animal;

let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal
rabbit.eats // true
```
![Pasted image 20220922131022.png](./images/Pasted%20image%2020220922131022.png)
#### `new` under the hood
1. creates a new plain object
2. sets the new object's prototype (`__proto__` or `[[Prototype]]` to reference the constructor's `prototype` object, aka the object referenced by `constructor.prototype`
3. value of `this` for use inside the function to point to the new object
4. Invokes the function. set properties & methods with reference to value of `this`, which is the new object
5. `new`returns the new object
	- Note: `new` will return the newly created object or the explictly stated return object in the constructor function. If the constructor returns a primitive value, `new` will ignore it. 

You can use `new` for pretty much any function EXCEPT...
> Note: Arrow functions cannot be constructor functions, because they use their surrounding context as the value of `this`

We like `new`. It simplifies the execution context ==> the implicit context is the new object you are creating.

Now that we know about constructors, we can add a constructor call with new as a third way to provide an implicit execution context. When you call a function with new, its implicit context is the new object.

#### Supplying arguments to constructor function
Method 1: passing in the args into the constructor (traditional)

```js
function Car(make, model, year, color, passengers, convertible, mileage) {
  this.make = make;
  this.model = model;
  this.year = year;
  this.color = color;
  this.passengers = passengers;
  this.convertible = convertible;
  this.mileage = mileage;
  this.started = false;

  this.drive = function() {
    this.started = true;
  };

  // rest of the methods
}
```
Method 2: Extrapolate properties to an object and pass it into the constructor
```js
let civicArgs = {
  make: 'Honda',
  model: 'Civic',
  year: 2016,
  color: 'black',
  passengers: 5,
  convertible: false,
  mileage: 16000
}
function Car(args) {
  this.make = args.make;
  this.model = args.model;
  this.year = args.year;
	...
}
let civic = new Car(civicArgs);
```
Method 3: using `Object.assign()` - all properties in args will get added to `Car`, including any excess
```js
function Car(args) {
	Object.assign(this, args);
	this. drive = function() {
		this.started = true;	
	}
	...
}
```


## Synergy: constructors + prototypes
Constructor functions can create numerous objects of a partiuclar type, with a template set of properties and methods. Yay!
**Problem**: While we probably want each instance to have it's own unique property values, it's redundant and memory intensive for each instance to have the same method repeated. 

**Solution**: Method delegation to Prototypes. Factor out the common method to a prototype, so that new instances can look up the prototype chain for the method, without having to save its own version of the method. 

##### How can we implement this? (Hack-y)
- Create an object  to serve as the prototype for new dog instances with the `bark` method.
- Within the constructor, add a line of code that sets the prototype of new instances to the `Dog.myPrototype` object
-  For addded readability: assign the prototype object to a property of the constructor `Dog` function

> Remember! JS functions are just objects, and objects can be assigned as a property to other objects. Here, we have a constructor `Dog`, with a property `Dog.myPrototype` with a method `bark`.

Result: `bark` is no longer defined on individual objects (each dog), but defined on the `[[Prototype]]` property.
```js
function Dog(name, breed, weight) {
  Object.setPrototypeOf(this, Dog.myPrototype);
  this.name = name;
  this.breed = breed;
  this.weight = weight;
}

Dog.myPrototype = {
  bark() {
    console.log(this.weight > 20 ? 'Woof!' : 'Yip!'); // now only one copy of bark method
  }
};

let maxi = new Dog('Maxi', 'German Shepherd', 32);
let dexter = new Dog('Dexter', 'Rottweiler', 50);
let biggie = new Dog('Biggie', 'Whippet', 9);
maxi.bark(); // 'Woof!'

maxi.hasOwnProperty('bark'); // false
dexter.hasOwnProperty('bark'); // false
biggie.hasOwnProperty('bark'); // false
Object.getPrototypeOf(maxi).bark === Dog.myPrototype.bark; // true
Object.getPrototypeOf(dexter).bark === Dog.myPrototype.bark; // true
Object.getPrototypeOf(biggie).bark === Dog.myPrototype.bark; // true

console.log(Dog); // [Function: Dog] { myPrototype: {bark: [Function: bark] } }
console.log(Object.getPrototypeOf(maxi)); // {bark: [Function: bark]} -- Dog.myPrototype

```

##### JS Implementation - `prototype` property!
JS has a built-in `prototype` property that comes with constructors (really every **function** has this property). When `new` is used with a constructor, JS sets the new object's prototype to the current value of the constructor's `prototype` property. 

```js
function UserCreator(name) {
	this.name = name;
}
UserCreator.prototype.voila = 'a property!'
let user = new UserCreator("Audry")

user.constructor // [Function: UserCreator]
user.__proto__ // { voila: 'a property!' } aka UserCreator.prototype
Object.getPrototypeOf(user) // same as above, just different method
UserCreator.prototype // { voila: 'a property!' }
```

Constructor creates an object that inherits from the constructor function's `prototype`, aka new object's prototype is a reference to constructor's `prototype` property. 

Let's slightly modify the example above, utilizing this fact above.
- instead of creating our homegrown `Dog.MyPrototype` property and defining the `bark` method, we just replace that with the constructor's built-in `Dog.prototype`

```js
function Dog(name, breed, weight) {
  // deleted Object.setPrototypeOf(this, Dog.myPrototype);
  this.name = name;
  this.breed = breed;
  this.weight = weight;
}
// Using prototype built-in
Dog.prototype.bark = function() {
  console.log(this.weight > 20 ? 'Woof!' : 'Yip!'); 
};

let maxi = new Dog('Maxi', 'German Shepherd', 32);
maxi.bark(); // 'Woof!'

let biggie = new Dog('Biggie', 'Whippet', 9);
biggie.bark(); // 'Yip!'
```

We can tie back a prototype to its constructor function and confirm that new dog instances are tied to the same constructor.

```js
Dog.prototype.constructor // [Function: Dog]

maxi.constructor // [Function: Dog]
maxi instanceof Dog // true

```

> Because `new` automatically sets the references for an instance object's constructor and `__proto__`, we should always strive to use the `new` keyword, even though the constructor function will run just fine without it (but there will be unexpected behavior)
### Constructor functions summary
- constructor functions (all functions) have a `prototype` property that is an object. We can leverage this object that `prototype` refers for prototypal inheritance for all new objects created with said constructor.
	- how? Because the `new` keyword automatically sets the new object's `__proto__` aka object prototype to the constructor function's `prototype` property.
- All objects have a `constructor` property for us to peep what constructor function created it.
- If we call an object method, it will go up the prototype chain:

check obj's "Own" properties --> check constructor's `constructor.prototype` object --> check the `prototype` property in the constructor's `__proto__`...etc.

### Constructor prototype vs object prototype
Every constructor function has a prototype object. 
- Constructor.prototype refers to this prototype object
- Constructors *do not inherit* from their prototype object. Only the objects that they create do. 
- Every JavaScript function has a `prototype` property, but it is only used when you call that function as a constructor function (with the `new` keyword)

Objects created by a constructor function inherit from the prototype object.
- by default when the object is created, the constructor function sets the object prototype to the contructor's prototype object.

###   Built-in constructors like `Array`, `Object`, `String` and `Number`
These constructors work like constructors for other objects; they're used with the new keyword to create objects.

###### `Array` constructor
- `new Array(1,2,3) // [1, 2, 3]`
- `new Array(3) // [<empty slot>...]`

`Array.prototype` - all array inherit from the object referenced. Every array can use methods defined in Array.prototype like map, forEach, etc. 

Static Array methods
- Array.isArray()
- Array.from - takes an array-like object and returns a new array with equivalent elemental values

> Array-like objects have a `length` property and provides indexed access to some of it's properties using bracket notation. This is useful when the DOM returns a node-tree.

Best practice is to use the `new` keyword to create new Objects and Arrays, although they work without it. 

**Almost all JavaScript objects, whether built-in or custom-created, inherit from `Object.prototype`, either directly or further down the prototype chain.**

```js
> Object.getPrototypeOf(Array.prototype) === Object.prototype
true
```
This means that Array types have access to Object methods.

###### String constructor
`String` objects are what allow string primitives to have methods! JavaScript has two kinds of strings: string primitives and String objects.

- `String` objects have methods like `length` and `toUpperCase()`. When you call a string method, JavaScript wraps the string primitive into a `String` object behind the scenes, runs the method, then returns the string primitive.  

> `String` function without the `new` keyword returns a new string


###### Number and Boolean constructors

`Number` and `Boolean` constructors work similarly to `String`, in that they have primitive and object forms. JS invisibly wraps primitives into objects to call methods and returns the primitive. 

When called without `new`, the `Number` function converts its argument to a number, and the `Boolean` function converts its argument to a boolean.

###### Date constructor
The Date constructor creates a [Date object](./JavaScript%20Dates.md), commonly called a date object, that represent a specific date and time. Calling Date without arguments returns a date object that represents the creation date and time of the object:
```js
> let now = new Date()
> now
2019-06-07T05:03:26.813Z
```

#### Borrowing Methods from Built-in Prototypes 

Since strings are array-like, we can borrow array methods from Array.prototype and set the execution context to our string using `call`:
```js
let string = 'EEE';
Array.prototype.every.call(string, char => char === 'E'); // => true
```

Why does this work? If we look at the implementation of Array.prototype.every, we see that it only requires `this` to have a `length` property and be indexable. String objects also have a `length` property and use index-based element access, so this works!
```js
Array.prototype.every = function(callback) {
  for (let index = 0; index < this.length; index++) {
    if (!callback(this[index])) return false;
  }

  return true;
};
```



---
Tags: [JavaScript](./JavaScript.md)
Reference:
Related: [Summary of Object Creation Patterns](./Summary%20of%20Object%20Creation%20Patterns.md) - [Objects](./Objects.md) - [Object Prototypes](./Object%20Prototypes.md)
