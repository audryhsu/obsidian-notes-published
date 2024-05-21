---
tags: 
aliases: 
---
Tags: [Go Lang](./Go%20Lang.md)
References: https://eli.thegreenplace.net/2020/embedding-in-go-part-1-structs-in-structs/

---
Embedding in [Go Lang](./Go%20Lang.md) follows the "composition over inheritance" OO philosophy. 

Three types of embedding:
1. [Struct in struct](Embedding%20in%20Go%20Lang.md#Struct%20in%20struct)
2. [Interface in interface](Embedding%20in%20Go%20Lang.md#Interface%20in%20interface)
3. [Interface in struct](Embedding%20in%20Go%20Lang.md#Interface%20in%20struct)

## Struct in [structs](./Go%20Lang%20Structures%20&%20Interfaces.md#Structs)

Why would you embed a struct in a struct:
1. Natural data partioning
	1.  `FileHeader` struct embedded in a `File` struct --> users may want to manipulate file headers separately from `File`.
	2. Could have just listed all of the header fields directly in File, but this abstraction is self-documenting 
```go
// debug/elf package
type FileHeader struct {
	Class Class
	Data Data
	Version Version
	...
}
type File struct {
	FileHeader
	Sections []Section
	Progs []*Prog
	closer io.Closer
	...
}
```
	 
> [!NOTE]
> There is a difference between embedding a struct *type*, and embedding an *instance* of a struct. 

2. "Inherit" properties and methods of the embedded type without writing forwarding methods
[Go Playground](https://goplay.tools/snippet/PNKseUSbbSC)
```go
type Engine struct {
	horsepower int
}

func (e Engine) Start() string {
	return "engine started!"
}

type Vehicle struct {
	model    string
	maxSpeed int
	Engine   // embed a struct type, NOT an instance of a struct
}

func main() {
	v := Vehicle{
		model:    "Subaru",
		maxSpeed: 150,
		// horsepower: 60, // compile error
		Engine: Engine{horsepower: 60}, // legal
	}
	// v.horsepower = 60 // legal

	fmt.Println(v.Start())
	fmt.Println(v.Engine.horsepower)
}
```

3. Useful way to implement interfaces, since methods of embedded struct are accessible
	1. `bufio.ReadWriter` type implements `io.ReadWriter` interface by embedding bufio.Reader and bufio.Writer structs, which in turn implement `io.Reader` and `io.Writer` interfaces.
```go
package bufio

// Reader implements io.Reader interface
type Reader struct {}
func (b *Reader) Read(p []byte) (n int, err error)

// Writer implements io.Writer interface
type Writer struct {}
func (b *Writer) Writer(p []byte) (n int, err error)

// ReadWriter type can implement io.ReadWriter interface
// How? it implements io.Reader and io.Writer interfaces via embedding structs
type ReadWriter struct {
	*Reader // Reader type implements io.Reader interface
	*Writer // Writer type implements io.Writer interface
}
```

## Interfaces in [interfaces](./Go%20Lang%20Structures%20&%20Interfaces.md#Interfaces)
Benefit:
1. Share capabilities and reducing duplication of method declarations. If we had to declare a `Read()` method for every interface in the standard library, ther would be LOTs of duplication (`io.ReadSeeker`, `io.ReadCloser`, `io.ReadWriteSeeker`, etc.)
2. Abstraction - If I know what a `io.Reader` interface is, then it's easier to reason about a `ReadWriter` interface and what it's capabilities are.

To define an interface for a type that's both a reader and writer, we can compose this new interface by embedding other interfaces. 
```go
package io

type Reader interface {
	Read(p []bytes) (n int, err error)
}
type Writer interface {
	Write(p []bytes) (n int, err error)
}
```

Option 1: This is explict, but hard to understand how it composes the `Reader` and `Writer` interface.
```go
type ReadWriter interface {
	Read(p []bytes) (n int, err error)
	Write(p []bytes) (n int, err error)
}
```

Option 2: Clearly states intent, in order to implement `ReadWriter`, you must implement `Reader` and `Writer`
```go
type ReadWriter interface {
	Reader
	Writer
}
```

Another example: The builtin `error` interface is embedded in the `net.Error` interface. This is clear to a reader that we can treat `net.Error` like an `error` without looking for the `Error()` method. 
```go
// builtin package
type error interface {
	Error() string
}
```
```go
// net package
type Error interface {
	error // built-in error interface
	Timeout() bool // is the error a timeout?
	Temporary() bool // is the error temporary
}
```


## Interfaces in structs

Person type implements Yeller interface 
[Playground](https://goplay.tools/snippet/yHzjoNInDfk)
```go
// A thing that can yell
type Yeller interface {
	yell(message string)
}

// takes a value that implements Yeller interface
func yell(y Yeller, message string) {
	fmt.Println(strings.ToUpper(message), "from yell func that takes a yeller")
}

type Person struct {}

// Person implements Yeller interface by adding a yell method
// Explicit, but person is tied to this implementation now.
func (p Person) yell(message string) {
	fmt.Println(strings.ToUpper(message), "from person method")
}

func main() {
	p := Person{}
	p.yell("hello")
	yell(p, "goodbye")
}
```

Person embeds a named Yeller interface
[Playground](https://goplay.tools/snippet/LTAAaqXzmn3)
```go
type Yeller interface {
	yell(message string)
}

func yell(y Yeller, message string) {
	fmt.Println(strings.ToUpper(message))
}
  
// Person has a named Yeller
type Person struct {
	myYeller Yeller
}

func main() {
	p := Person{}
	// yell(p, "hello from yell func that takes a yeller") // cannot use type Person as type Yeller in argument
	yell(p.myYeller, "use person's yeller") // explicitly tell yell to use Person's Yeller
}
```

Person emebds an anonymous Yeller interface
[Playground](https://goplay.tools/snippet/63QhoiUkEUl)
```go
type Yeller interface {
	yell(message string)
}

func yell(y Yeller, message string) {
	fmt.Println(strings.ToUpper(message), "from yell func that takes a yeller")
}

// Embed an anonymous Yeller
type Person struct {
	Yeller
}

func main() {
	p := Person{}
    // Person is a Yeller, passing the yell call to the wrapped Yeller.
	yell(p, "goodbye")
	// works because Person implements Yeller via embedding interface
	
}
```