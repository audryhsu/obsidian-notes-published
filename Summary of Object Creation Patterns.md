

# Summary of [Summary of Object Creation Patterns](Summary%20of%20Object%20Creation%20Patterns.md)
#### 1. **Factory Pattern** - automating "copy and paste" 
❌ No prototype inheritance
	1. Define a function that returns an object literal
	2. New objects have their own copy of methods, which may not be memory efficient
	3. Cannot trace source of object / can't tell object types (the `getPrototypeOf` would return `Object.prototype` since they are all new object literals and no inheritance is used)

Disadvantages:
- Every object created with a factory function has a *full* copy of all methods, which can be redundant or memory intensive. 
- Unlike constructors, you cannot tell which factory function created a given object -- hard to confirm correct object type
Advantages: 
- The factory pattern does have one advantage: it allows objects to be created with a private state. 
- Note: Some folks like to use factory pattern + mixins

#### 2.**Using Constructor Functions**
*Note: constructors are mostly used as part of the pseudo-classical pattern*
Looks similar to factory function but new object creation is abstracted away with the `new` keyword & use of `this` to set obj properties and methods
✅ prototype inheritance
	1. New objects will still have their own copy of methods, but source can be traced via each instance's `constructor` property 
	2. Use `new` keyword creates constructor's `[[Prototype]]` prop and auto sets new objects' `__proto__` to the constructor function's `[[Prototype]]` prop.
	3. explicitly return of an object is optional (since `new` takes care of this). It will ignore returned primitive values.

- **constructors** - looks and acts like a factory function, but 
	- uses the `new` keyword to create new objects,
	-  `this` to set object properties & methods, and
	-  do not have to explicitly return a  value (this it taken care of by `new` under the hood)
	-  `instanceof` operator will identify the constructor function that created an object


> Arrow functions **cannot** be used as constructor functions nor can methods defined with concise syntax

#### 3. ES6 Classes
ES6 classes provide a cleaner, more compact alternative to constructors and prototypes. As with functions, they are first-class citizens and come in the form of declarations and expressions. Functionally, ==classes behave almost identically to the constructors and prototypes ([pseudo-classical pattern](Summary%20of%20Object%20Creation%20Patterns.md#4%20Pseudo-classical%20inheritance%20-%20constructors%20prototype%20patterns) )they aim to replace==.

1. `super`- when called inside `constructor` method, it refers to constructor method of the parent class. It performs the same thing as invoking `Rectangle.call(this, args...)`:
```js
class Rectangle {
 constructor(length, width) {
this.length = length;
this.width = width;
 // methods

class Square extends Rectangle {
  constructor(size) {
	super(size, size); // equivalent to Rectangle.call(this, size size)
  }
}
```
[📝See the article you wrote here](./Prototypes%20vs%20Classes.md)
	
#### 4. **Pseudo-classical inheritance** - constructors + prototype patterns
##### Nomenclature
 The combination of [constructors](./Constructor%20Functions.md) and [prototypes](./Object%20Prototypes.md) gives us something that resembles a class, a construct used in classical OOP languages like Java, Python, and Ruby. A class is a blueprint for creating objects. Traditional OOP languages use classes to create distinct objects of a particular type and give those objects the behaviors and state that they need.

The pseudo-classical object creation pattern generates objects using a **constructor function** that defines state and a **prototype object** that defines shared behaviors.

The term "pseudo-classical" refers to the fact that the pattern mimics classes from other OO languages but doesn't actually use classes. We can create sub-type objects that inherit from a super-type object (the object prototype). 

If the function is used as a constructor, the returned object's [Prototype](Prototype.md) will reference the constructor's prototype property. That lets us set properties on the constructor's prototype object so that all objects created by the constructor will share them. We call this the pseudo-classical pattern of object creation.
##### Workflow
1. Solves efficiency problem of #1 and #2 by leveraging the prototypal inheritance but limited by **single inheritance**
2. Clear link from new object's and constructor function via the `constructor` property
3. Shared behavior lives in the prototype and is delegated instead of being copied into each new object.
4. Use `new` keyword auto sets new objects' `[[Prototype]]` to the constructor function's `[[Prototype]]` prop.
5. When sub-typing, will need to restore the `constructor` property on the subclass's `prototype` prop to point to the subclass constructor function via reassignment.
	1. Not make or break, but if you do not, any new objs created from subclass will have their `constructor` properties pointing at the superclass, instead of the subclass. Remember, `constructor` helps leave a bread trail of an obj's type. 


![Pasted image 20211122190921.png](./images/Pasted%20image%2020211122190921.png)

#### 5. Objects Linking to Other Objects -  delegation pattern
**OLOO** - bulk create objects based on one common object
	- Uses a simple object as the blueprint "prototype" and `Object.create()` instead of a constructor function
	2. No constructors, instantiate instance properties with a defined `init` method inside blueprint object
		- Since no constructor function, new instance's `constructor` is the global object, and references to `prototype` property will refer to the `Object.prototype`
	3. Optional use if `init` to short-cut initialize new objects with their data properties
**4. Very similar to Factory pattern, but more efficient and takes advantage of prototypal inheritance.**



# Object creation snippets
1. **Object Factory Pattern** - automating "copy and paste" given a template
	Steps:
		1. Write a function that returns an object literal
```js
function createPerson(first, last) {
	return {
		person.first: first,
		person.last: last,
		person.fullName() {
			// some method
		},
	}
}
```
2. **ES6 Classical Inheritance** - uses `class`, `extends`, `super`, `new`; Same as Pseudoclassical, just abstracted away
```js

```
3. **Pseudo-classical inheritance** - constructor + prototype patterns & `new` keyword
	**Steps**:
		1. Define constructor function for superclass
		2. Constructor function takes class properties as args
		3. Define methods inside constructor's `prototype` property 
		4. Define subclass constructor functions using same args as superclass PLUS any subclass specific properties
			1. Reuse superclass' constructor function to create new subclass objects and allow inheritance of superclass methods
			2. **Be sure to call superclass constructor binded to `this`** to have subclass *"inherit" superclass properties*
		5. Reassign subclass `prototype` obj to superclass' `prototype` obj using  `Object.create()` to *"inherit" superclass methods*
			1. `subclass instanceof superclass` // true
		6. Reassign subclass `prototype.constructor` to subclass constructor function (e.g. `Professor.prototype.constructor = Professor`)
		7. Define any subclass methods within `prototype` property
		
```js
function Person(firstName, lastName, age, gender) {
  this.firstName = firstName;
  this.lastName = lastName;
  this.age = age;
  this.gender = gender;
}
Person.prototype.fullName = function () {
  return `${this.firstName} ${this.lastName}`;
}


function Student(firstName, lastName, age, gender, degree) {
  return Person.call(this, firstName, lastName, age, gender) // use superclass constructor to inherit properties
  this.degree = degree;
}
Student.prototype = Object.create(Person.prototype) // reassign subclass prototype obj to a new obj with it's [[Prototype]] prop pointing to superclass prototype obj to inherit Person methods

Student.prototype.study = function () {
  console.log("Gotta catch em all!");
};

Student.prototype.constructor = Student; // because of prototype obj reassignment, need to fix the constructor property to properly point to the Student constructor func

function GraduateStudent(firstName, lastName, age, gender, degree, graduateDegree) {
  return Student.call(this, firstName, lastName, age, gender, degree)
  this.graduateDegree = graduateDegree;
}

GraduateStudent.prototype = Object.create(Student.prototype);
GraduateStudent.prototype.constructor = GraduateStudent;

let graduateStudent = new GraduateStudent('foo', 'bar', 21, 'gender', 'BS Industrial Engineering', 'MS Industrial Engineering');

console.log(graduateStudent instanceof Person); // true
console.log(graduateStudent instanceof Student); // true
console.log(graduateStudent instanceof GraduateStudent); // true

```

1. **OLOO prototypal inheritance** - "object-centric: delegation pattern rather than inheritance pattern"
**Objects Linking to Other Objects** uses prototypes and extracts properties common to all objects of the same type to a prototype object, so that all new objects can inherit from it using `Object.create`
- It's very similar to factory function except that instead of using a function to return new objects, it uses Object.create to return new objects with prototype inheritance.

We can use `init` method on the prototype object to automate the creation of properties. 

Create a carPrototype
```js
let carPrototype = {
  start: function() {
    this.started = true;
  },

  stop: function() {
    this.started = false;
  },
  init(make, model, year) { 
  	this.make = make; 
	this.model = model; 
	this.year = year;
	return this;
	},
};

let car1 = Object.create(carPrototype).car1.init('Toyota', 'Corolla', 2016) 
// create a new object with carPrototype set as the __proto__ reference.

```
This produces the same object as factory function method, but is **more memory efficient** to have all objects inherit from a single prototype object rather than a factory function create copies of the instance methods unique to each object. 
- OLOO has a clear prototypal chain that we can reference using the `__proto__` property.

## Study Facts
- **`instanceof` operator** tests to see if the `[[Prototype]]` property of a constructor appears anywhere in the prototype chain of an object. The return value is a boolean value.
- use `Object.setPrototypeOf(targetobj, prototype)` or `newObj = Object.create(prototype)`


- `Object.assign()` - merges two or more objects -- **MUTATES** target obj.
	- can be used to give a class/module access to method or properties of a mix in object by including the mix in object in the class's prototype. This allows us to invoke any of the mix in obj's methods on any instance of the class.
	- Technically, `Object.assign(target, source)` copies all **enumerable own properties** from one or more source objects to a target object and returns the mutated target object.  

- `Function.prototype.call(this, arg1..)` - calls a function with a given `this` value and also optionally accepts arguments.
	- BLUF: this allows a method to be written once, and then inherit it in another object without having to rewrite the method for the new object.


---
Reference: [JavaScript Design Patterns | Medium](https://medium.com/launch-school/javascript-design-patterns-building-a-mental-model-68c2d4356538)
