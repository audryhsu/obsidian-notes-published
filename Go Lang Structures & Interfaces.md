---
creation date: 2022-03-22 09:04
aliases: 
tags: 🖥️ golang
---
Tags: [Programming](Programming.md) - [Go Lang](./Go%20Lang.md) 
Reference: [Interfaces by Alex Edwards](https://www.alexedwards.net/blog/interfaces-explained)

---

Go is not object oriented language, thus it doesn't have inheritance. It uses **structures**, which can be associated with methods. Structures allow us to make custom types, and we can associate structs with similar behavior together using **interfaces** (named collections of methods).

Note that you may need *pointers* to structs more often than copies of their values in order to be able to mutate their properties. [A function](./Go%20Lang%20Snippets.md#Passing%20pointers%20of%20Structs) that expects a `*Saiyan` must receive a pointer to a Saiyan struct.

>[!Structs vs Interfaces]
> Structs define *properties* of a concept, while interfaces define *shared behaviors* between different struct types.  

# Structs
**Structs are a collection fields** that hold data. Structs define "has a" relationships. 
##### Defining and declaring Structs
```go
type Saiyan struct {
  Name string
  Power int
}
```

We can declare a new struct of type Saiyan with or without values.
```go
goku := Saiyan{
	Name: "Goku",
	Power: 9000,
} //or
goku := Saiyan{Name: "Goku"}
goku.Power = 9000
// or initialize without values
goku := Saiyan{} 
```
[To create new instances of a struct](./Go%20Lang%20Snippets.md#Struct%20Constructors), We can use either the `new` constructor or factory functions

##### Methods
We can associate a method with a structure by specifying that type `*Saiyan` is the **receiver** of the Super method, and can call it with dot notation.

Note that defining  methods are slightly different from regular named functions, because it **receives** a struct type first, then returns the name of the method

![Go Lang Structures & Interfaces_2022-03-23 11.09.17.excalidraw](./images/Go%20Lang%20Structures%20&%20Interfaces_2022-03-23%2011.09.17.svg)
```go
func (s *Saiyan) LevelUp() {
  s.Power += 50
  s.Hair = "gold"
}
goku.Super() // will print 9050
```
- Note that this is different than defining a function that takes a Saiyan as an argument. 

##### Embedded Type
Fields in a structure can be of any time, including arrays, maps, interfaces, and other structures. Go supports *composition*, the act of including one structure into another (very similar to using  [mixins](./Object%20Oriented%20Programming%20(OOP).md#Use%20Mix%20ins%20when%20one%20object%20'has%20is%20a'%20certain%20property))

Embedded Types use a "is a" relationship. We can define a `Person` struct with an `Greet` method. A Saiyan **is a** Person that can greet.

![Go Lang Structures & Interfaces_2022-03-23 11.06.30.excalidraw](./images/Go%20Lang%20Structures%20&%20Interfaces_2022-03-23%2011.06.30.svg)

- Saiyan structure has a field of type `*Person`.
- `Person` struct can be accessed using the type name or directly calling any `Person` methods on goku
	- `goku` has access to the `Person.Greet` method, as well as `Person.name`

# Interfaces
### Basics
**Interfaces are a collection of methods**. 
- Interfaces contain a list of methods a type must have in order to "implement" the interface. 
- Interfaces are how Go implements [polymorphism](./Object%20Oriented%20Programming%20(OOP).md#Polymorphism). 
	- The [classic example](./Go%20Lang%20Snippets.md#Shapes%20Interfaces): rectangles and circles both have area methods, so we can implement a Shapes interface.
With Interfaces, **we care about shared behaviors** to create a type rather than same properties (e.g. in [Structs](Go%20Lang%20Structures%20&%20Interfaces.md#Structs)). 
> [!example]- Jedis and Saiyans are Fighters
>The power of interfaces at first doesn't seem obvious, since we have to define a `LevelUp` method for `Jedi` and `Saiyans` anyways.
>**The true power of interfaces is that we created a new type!** The `Fighter` interface that allow us to work with `Saiyans` and `Jedi` simultaneously without needing to know internals of Saiyan or Jedi structures.
>- We can create a slice of `Fighters`, calculate Power on all `Fighters`, pass `Fighters` into function, use `Fighters` as an embedded type for Heros struct... etc.
> ![Go Lang Structures & Interfaces_2022-03-23 11.14.36.excalidraw](./images/Go%20Lang%20Structures%20&%20Interfaces_2022-03-23%2011.14.36.svg) 
> ![Go Lang Structures & Interfaces_2022-03-23 11.16.40.excalidraw](./images/Go%20Lang%20Structures%20&%20Interfaces_2022-03-23%2011.16.40.svg)

> [!info] Blank interfaces
> Any and every object satisfies the empty `interface{}`. However, by definition, it doesn't have any methods or properties. If you want to perform any operations on `interface{]` value, you must type assert back to their underlying type.
> ```go
> person := make(map[string]interface{}, 0)
> person["age"] = 21
> 
>age, ok := person["age"].(int)
>	if !ok {
>		log.Fatal("could not assert value to int")
>		return
>	}
>
>	person["age"] = age + 1
>```
> It is generally safer and more performant to use concrete types instead of empty interfaces in my code.
> > [!faq]- Type assertions
> > Type assertion is a way of telling the complier what the underlying type of something is to access methods and properties. 
> > 

### Common and useful interface types
-   [`builtin.Error`](https://golang.org/pkg/builtin/#error)
	- `Error()` and return a `string`
-   [`fmt.Stringer`](https://golang.org/pkg/fmt/#Stringer)
	- `String()` and return a `string`
	- debugging and printing values
-   [`io.Reader`](https://golang.org/pkg/io/#Reader) &   [`io.Writer`](https://golang.org/pkg/io/#Writer)
	- `Read([]bytes) int, err`
	- `Write([]bytes)`
	- reading and writing to streams of data like files, network connections, byte buffers
-   [`io.ReadWriteCloser`](https://golang.org/pkg/io/#ReadWriteCloser)
-   [`http.ResponseWriter`](https://golang.org/pkg/net/http/#ResponseWriter)
	- 
-   [`http.Handler`](https://golang.org/pkg/net/http/#Handler) - define HTTP handlers and middleware
	- `ServeHTTP(http.ResponseWriter, *http.Request)`




# Examples
###### Structs
```go
// create a custom type named Point, with properties
type Point struct {
	x int32
	y int32
}

// use Point struct inside of Circle
type Circle struct {
	radius float64
	center Point
}
func main() {
	p1 := Point{4, 5}
	c1 := Circle{2.41, p1} // c1 is {2.41 {4 5}}

	fmt.Println(c1.center.x) //4
	c1.center.x = 3000
	fmt.Println(c1.center.x) 
	fmt.Println(c1) // {2.41 {3000 5}}
}
```
###### Composition / Embedded Type
```go
type Saiyan struct {
	*Person 
	Power int
}
type Person struct {
	Name string
}
func (p *Person) Greet() {
	fmt.Printf("Hi, I'm %s\n", p.Name)
}

func main() {
	goku := &Saiyan{
		Person: &Person{"Goku"}, //pass a Pointer to a Person struct
		Power:  9001,
	}
	goku.Greet() // Hi, I'm Goku
	fmt.Println(goku.Name) // Goku
	fmt.Println(goku.Person.Name) // Goku

}
```

###### Structs, Embedded Types, and Interfaces, oh my!
- `Saiyans` and `Jedi` are both a type of `Person`.
- We can use polymorphism to `LevelUp` `Saiyans` and `Jedi` through the `Fighter` interface.
![Go Lang Structures & Interfaces_2022-03-23 11.20.10.excalidraw](./images/Go%20Lang%20Structures%20&%20Interfaces_2022-03-23%2011.20.10.svg)

[✂️Full Code](./Go%20Lang%20Snippets.md#Jedi%20Saiyan%20Person%20Fighters)
