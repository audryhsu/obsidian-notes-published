# Subclassing with Prototypes
This was covered in example 7 of Ryan's video. There is an extra step that we must do when we create a "subclass" with prototypes

We can implement a subclass-type pattern by updating a constructor function's `prototype` property to the desired super type (or parent class).

Squares are a special kind of rectangle. Instead of duplicating code (rectangles and squares share length, width, and the same formula to calculate area), we can sub-type Rectangle.

```js
function Square(size) {
  this.length = size;
  this.width = size;
}

Square.prototype = Object.create(Rectangle.prototype); // set Square's prototype property to Rectangle

Square.prototype.toString = function() {
  return `[Square ${this.length} x ${this.width}]`;
};

let sqr = new Square(5);
sqr.getArea();     // => 25
sqr.toString();    // => "[Square 5 x 5]"
```

All objects created by Square constructor inherit from Square.prototype, which now inherit from Rectangle.prototype so that they share methods. 

Because we reassigned Square.prototype to the object at Rectangle.prototype, and the constructor property for Rectangle.prototype points to Rectangle function, sqr.constructor erroneously points to Rectangle.

We need to **reassign** the constructor property to the Square function.

```js
Square.prototype.constructor = Square;
```

Full code implementation:
```js
function Rectangle(length, width) {
  this.length = length;
  this.width = width;
}

Rectangle.prototype.getArea = function() {
  return this.length * this.width;
};

Rectangle.prototype.toString = function() {
  return `[Rectangle ${this.length} x ${this.width}]`;
};

function Square(size) {
  Rectangle.call(this, size, size);
}

Square.prototype = Object.create(Rectangle.prototype);
Square.prototype.constructor = Square;

Square.prototype.toString = function() {
  return `[Square ${this.length} x ${this.width}]`;
};
```
# Subclassing with class declarations
We looked at how we can extend a subclass in [Prototypes vs Classes](./Prototypes%20vs%20Classes.md). We can spend some more time diving into the new keywords here.

```js
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  }

  getArea() {
    return this.length * this.width;
  }

  toString() {
    return `[Rectangle ${this.width * this.length}]`;
  }
}

class Square extends Rectangle {
  constructor(size) {
    super(size, size);
  }

  toString() {
    return `[Square] ${this.width * this.length}`;
  }
}
```

the Square constructor calls `super` inside the `constructor` method. `super` keyword refers to the constructor method of the parent class, which means that `super(size, size)` is equivalent to `Rectangle.call(this, size, size)` in our constructor + prototype pattern. 
- `super` is not required, but often used to make sure object properties are set. If you do call `super` in a subclass's constructor, you must call it before you use `this` in that constructor.

![Pasted image 20220922121351.png](./images/Pasted%20image%2020220922121351.png)

### Class expression
Because classes are just functions, we can also create classes with function expressions (same thing, different look):

```js
let Person = class {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  sayName() {
    console.log(`My name is ${this.name}.`);
  }
};

let Student = class extends Person {
  constructor(name, age, semester) {
    super(name, age);
    this.semester = semester;
  }

  enrollInCourse(courseNumber) {
    console.log(`${this.name} has enrolled in course ${courseNumber}.`);
  }
};

let student = new Student('Kim', 22, 'Fall');
student.sayName(); // logs 'My name is Kim.'
student.enrollInCourse('JS120'); // logs 'Kim has enrolled in course JS120.'
```

# Single Inheritence Problem
Javascript objects can only inherit from one object/prototype chain, and classes can only extend from one other class. While other languages allow classes to inherit from multiple classes (non-linear) aka ***multiple inheritence***, we must use **mix ins** to accomodate this relationship modelling in JavaScript.

>Mix ins is a pattern that adds methods and properties from one object to another via copying with `Object.assign`. It is **not delegation**. 

Example:  Dogs can swim, and fish can swim, but they are part of different prototypal chains. We would end up duplicating the swim method in both places. 
![Pasted image 20211122094747.png](./images/Pasted%20image%2020211122094747.png)

#### How to implement mix ins
1. Create an object that defines one or more methods that can be "mixed in" to a class. (usually the functionality that is shared by multiple classes)
2. Use `Object.assign()` to copy over properties from the mix-in object (source) to the class object's prototype (target)

Flying and swimmable birds:

```js
const Swimmable = {
  swim() {}
}

class Bird {}

class FlyingBird extends Bird {
  fly() {}
}

class Stork extends FlyingBird {}

class Penguin extends Bird {}
Object.assign(Penguin.prototype, Swimmable);

class Goose extends FlyingBird {}
Object.assign(Goose.prototype, Swimmable);

console.log(Goose); // [class Goose extends FlyingBird]
console.log(Goose.__proto__); // [class FlyingBird extends Bird]
console.log(Goose.prototype); //FlyingBird { swim: [Function: swim] }
console.log(Goose.prototype.constructor); //[class Goose extends FlyingBird]
```

- we could use factory functions instead of classical inheritence, but we would face the same pitfalls: 1) factory functions create a new copy of *all* methods, both the mix-in methods and the methods common to the object type. This is not memory optimal, and 2) cannot determine the type of an object created with a factory function (`typeof` is just a regular function.)
Launch school recommends using mix-ins with the classical inheritence pattern:
1. When an object is a definitely a sub-type of another, inheritance works really well("a penguin is a swimming bird")
2. Use mix-ins to solve the problem of granting capability to objects in a more ad-hoc manner when some sub-types need it. 

# Polymorphism
**Polymorphism** refers to ability of objects with different types to respond in different ways to the same message/ method invocation. this means data of different types can respond to a common interface. This is crucial to OOP to lead to more maintainable code.

- polymorphism through inheritence
	- Two different subclass object types can respond to the same method but result is different because one of the subclasses/object types overrides a method (aka has it's own defined method) inherited from a superclass.

- polymorphism through duck typing
	- **duck typing** is when objects of different *unrelated* types both respond to the same method name. We don't care what class or type of object, just that it quacks like a duck, so we treat it as a duck. This is just an informal way to group objects (e.g. "clickable" objects in the DOM) 
	- classes and constructors are formal ways to group objects.
	- this seems like just a smart way to organize objects so that we can call a method to run like a batch process -- we can write less code with conditionals that would otherwise require us to call specifc method names and their specific arguments for each object type/class/subclass

##### Example of duck-typing
Instead of calling each vendor's unique method to prepare for the wedding, we ascribe each of them a `prepare` method that has a nested function to call their unique preparation funcationlity. So when it's show time for the wedding, we can mobilize all vendors in one batch by calling the `Wedding.prepare` method 
```js
class Chef {
  prepare(wedding) {
    this.prepareFood(wedding.guests);
  }

  prepareFood(guests) {
    // implementation
  }
}

class Decorator {
  prepare(wedding) {
    this.decoratePlace(wedding.flowers);
  }

  decoratePlace(flowers) {
    // implementation
  }
}

class Musician {
  prepare(wedding) {
    this.preparePerformance(wedding.songs);
  }

  preparePerformance(songs) {
    // implementation
  }
}

class Wedding {
  constructor(guests, flowers, songs) {
    this.guests = guests;
    this.flowers = flowers;
    this.songs = songs;
  }

  prepare(preparers) {
    preparers.forEach(preparer => {
      preparer.prepare(this);
    });
  }
}
```

---
Tags: [JavaScript](./JavaScript.md) - [Context Loss](Context%20Loss.md)
Reference:
Related: 