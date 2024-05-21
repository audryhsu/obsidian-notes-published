---
creation date: 2022-03-14 10:58
aliases: 
tags: 🖥️
---
# Object Oriented Programming with JavaScript
---
Index for JavaScript OOP:
- [Constructor Functions](./Constructor%20Functions.md)
- [Summary of Object Creation Patterns](./Summary%20of%20Object%20Creation%20Patterns.md)
- [Prototypes vs Classes](./Prototypes%20vs%20Classes.md)
- [Subclassing and Code Reuse Patterns](./Subclassing%20and%20Code%20Reuse%20Patterns.md)

What we've done thus far in JS101 is procedural programming - declaring variables, using conditions and loops and function calls to accomplish tasks. We used some [functional programming](./Functional%20Programming.md) techniques (think passing functions to higher order functions like `map` and `filter`). 

Now, let's do a mind shift towards the OOP paradigm, in which we model our software problems after real world relationships and objects. 

Objects can be described in terms of their **state** (aka properties) and **behavior**. 

#### Why learn OOP?
**Advantages of OOP:**
1. OOP can be more intuitive for breaking down complex coding problems by recontextualizing the problem into **real-world objects and relationships**
	- Modeling objects based on real-world nouns
	-  By abstracting away procedural programming into OOP, code becomes **simpler** and easier to understand. Problem solving in terms of objects we are familiar is easier for human software engineers to problem solve around. This is why using sensical and descriptive nouns to represent objects help readability and maintain-ability of code!
2. Reduces dependencies by modularizing code into objects via [encapsulation](./Encapsulation.md)
	- For larger programs, OOP is more easily scalable, as there are less dependencies throughout the code based that make it more flexible, easier to understand, and easier to change/update. 
**Disadvantages of OOP**
- OOP programs aren't necessarily always more efficient -- they usually require more memory, diskspace, and computing power. Also, a good OOP program will take *planning*.


One weak point of JS: there doesn't seem to be built in functionality for private properties/methods, as opposed to other OOP languages. 
- [Summary of Object Creation Patterns > Summary of Object Creation Patterns](./Summary%20of%20Object%20Creation%20Patterns.md#Summary%20of%20Object%20Creation%20Patterns)
- [LS Summary Object Creation Methods](https://launchschool.com/lessons/e3c64e3f/assignments/d9d4b6a3)

### Methods and properties
instance and static methods and properties
- **instances** refer to individual object of a specific data type. Another way to refer to object created using any of the object creation patterns, including factory functions (even though we cannot directly trace the type using `typeof` or prototype, but we know a factory function is going to create several objects that represent a specific object type.)
- **instance properties** - belong to a specific instance created by the constructor function.
- **instance methods** - usually defined in object's prototype object but still operate on individual instances. Best practice is to invoke these methods from the instance.
- **static** properties - defined and accessed directly on the constructor function, not on an instance or prototype. These are usually pertinet to all instances of a type.
- **static methods** -` Object.assign`, `Array.isArray` are examples of static methods!

###  Polymorphism
- ability of objects with different types to respond in different ways to the same method invocation
- when two or more objects have the method with the same name and we invoke it, we don't care what type of object is calling it and if the outputs are different.
>[!INFO]
>Polymorphism is most common through inheritance **when subclass methods override** a superclass method (e.g. Animals can move, and subtypes of animals can "swim", "run", "climb", but they all have their own move method)
- Another example is `Object.prototype.toString()` method - we can call this on array objects or date objects, but the output is different 
```
> [1, 2, 3].toString()
'1,2,3'

> (new Date()).toString()
'Fri Jun 28 2019 20:50:13 GMT-0700 (Pacific Daylight Time)'
```


**Duck typing** is another way polymorphism occurs when objects of ***unrelated types*** respond to the same method name. 
>As long as objects use the same method name and take same number of arguments, we can group those objects together for duck typing.

Wedding class has a prepare method, which can iterate through all of the Vendor classes and invoke each of their own version of prepare.

🧠 Mental model: an orchestra of different instrument sections can all respond to the same conductor's movement ("downbeat") but play a different sound/role. We don't care how the instruments implement their sound, we just care that we have a symphony.


### Collaborator objects
- Objects that help provide state within another object are called **collaborator objects**, or more simply, **collaborators**.Two objects have a collaborator relationship if one of them is part of the state of the other. (think player and dealer are collaborators part of the Twenty One Game.)
- In OOP, collaborator objects represent connections between different classes.

Example: `Pete` object has a pet property that references `cat` We can get access to `cat` via `pete.pet`. 
```js
let cat = {
  name: 'Fluffy',
};

let pete = {
  name: 'Pete',
  pet: cat,

  printName() {
    console.log(`My name is ${this.name}!`);
    console.log(`My pet's name is ${this.pet.name}`);
  },
};
```
### Single vs multiple inheritance
- JS has single inheritance, which means objects can inherit from only one object and classes can only extend one other class.
- To solve this, use mix-ins
- Other languages allow classes to inherit from multiple classes

### Prototype inheritance vs Pseudo-classical inheritance
[| Pseudo-classical](./Summary%20of%20Object%20Creation%20Patterns.md#4%20Pseudo-classical%20inheritance%20-%20constructors%20prototype%20patterns) object construction is aka the **constructor/prototype** pattern.

Every object has an internal `[[Prototype]]` property, which has a ***reference*** to it's [object prototype](./Object%20Prototypes.md). 
- `Object.create` auto sets the newObj's `[[Prototype]]` to the prototype object passed in. 
	- in Pseudo-classical pattern, we can designate a sub/super class relationship by setting the subclass constructor function's prototype to the Superclass's constructor's prototype object. 
- Inheritance works because if the prototype obj is mutated, the reference to the obj allows the changes to be observable in the inheriting object.
- `Object.getPrototypeOf(obj)` to access the `[[Prototype]]` internal property.


### Mix-ins vs. inheritance

> [!NOTE] Composition vs Inheritance
> 
> Use mixins (composition) when there is a "has a" relationship. Inheritance models an "is a" relationship. A horse *is a* type of animal, and *has a* Tail. 
> 
> Using composition prevents unnecessary coupling, as changes in the superclass will be seen in all subclasses.
> 

==**Use Mix ins when one object 'has/is a' certain property**==
- **Mix ins** is a pattern that adds methods/properties from one object to another. A mix-in object defines one or more methods that can be "mixed in" to a class. This grants a class access to all of the methods in the object.
- It's a point-solution to share functionality between objects that otherwise wouldn't be part of the prototype inheritance chain or class. 
- This solves the problem of JS lacking multiple inheritance short of duplicating properties

This is NOT delegation with prototypes, it copies and pastes properties from a mix in object to our target object's `[[prototype]]` using `Object.assign()`

**When to use**: works well when there is a **has a** relationship, or just shared functionality
**Example**: "A penguin *is a* swimming bird". Swimming is a capability that penguins, while flying is a capability storks have.
**Downsides**: 
- Like factory functions, every new object receives a new copy of all it's methods including mix-in and instance methods.

###### Use inheritance when one object 'is a type of' another object 
**==Inheritance works well when one object type is a subtype of another object.==** 

Example: We move the shared `range()` method from the WheeledVehicle class to the Moveable mix-in and copied the method to WheeledVhicle and Catamaran classes.

```js
const Moveable = {
  range() {
    return this.fuelCap * this.fuelEfficiency; // moved to mix in object
  }
};

class WheeledVehicle {
  constructor(tirePressure, kmTravelledPerLiter, fuelCapInLiter) {
    this.tires = tirePressure;
    this.fuelEfficiency = kmTravelledPerLiter;
    this.fuelCap = fuelCapInLiter;
	...
}

Object.assign(WheeledVehicle.prototype, Moveable); // copy properties

class Auto extends WheeledVehicle {}

class Motorcycle extends WheeledVehicle {}

class Catamaran {
  constructor(propellerCount, hullCount, kmTravelledPerLiter, fuelCapInLiter) {
    this.propellerCount = propellerCount;
    this.hullCount = hullCount;
    this.fuelEfficiency = kmTravelledPerLiter;
    this.fuelCap = fuelCapInLiter;
  }
}

Object.assign(Catamaran.prototype, Moveable);
  
```