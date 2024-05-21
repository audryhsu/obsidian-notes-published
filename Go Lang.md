---
creation date: 2022-03-15 16:01
aliases: 
tags: 🖥️
---
Tags: [Programming](Programming.md) - [Go Lang](Go%20Lang.md)
Reference: [Golang Book](https://www.golang-book.com/books/intro/13#section1) | [Go by Example](https://gobyexample.com/) | [Little Go Book](https://www.openmymind.net/The-Little-Go-Book/)

---
#### Index
[🔗 Cheatsheet](https://devhints.io/go)
- [Go Lang Packages](./Go%20Lang%20Packages.md)
- [Go Types](./Go%20Types.md)
- [Go Lang Pointers](./Go%20Lang%20Pointers.md)
- [Go Lang Error Handling](./Go%20Lang%20Error%20Handling.md)
- [Go Lang Strings](./Go%20Lang%20Strings.md)
- [Go Lang Goroutines and Channels](./notes/Go%20Lang%20Goroutines%20and%20Channels.md)
- [Golang Context](./Golang%20Context.md)
- [Go Lang slices](./Go%20Lang%20slices.md)
- [Go HTTP services Patterns](https://pace.dev/blog/2018/05/09/how-I-write-http-services-after-eight-years.html)
- [DigitalOcean - How to code in Go Tutorial Series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go)
- [Go Lang Defer, Panic, Recover](./Go%20Lang%20Defer,%20Panic,%20Recover.md)
- [Go Lang Generics](./Go%20Lang%20Generics.md)
- [Go Lang Testing](./Go%20Lang%20Testing.md)
- [Go Lang Structures & Interfaces](./Go%20Lang%20Structures%20&%20Interfaces.md)
- [Embedding in Go Lang](./Embedding%20in%20Go%20Lang.md)

Format go code: `go fmt path/to/your/package`

> [!Tip] Installing Go from the website vs homebrew 
> - homebrew installs things in the `/opt/homebrew/bin`
> - Go website installs Go in `/usr/local/go`
> - use `which go` to see where your older version of Go is located (culprit for me was it was in homebrew binaries)
> - delete that binary, and then `which go` should update to the new version

#### Background
Go is a compiled, general-purpose language designed with systems programming in mind. It is strongly typed and garbage-collected and has explicit support for concurrent programming. Programs are constructed from packages, whose properties allow efficient management of dependencies.

Compilation is the process of translating source code (I write) into lower level language, like assembly. Compiled languages tend to run faster and the executable can be run without any additional dependencies.

Statically typed means variables must be of a specific type. Advantage is that a compiler is able to detect problems beyond syntax mistakes and make further optimizations.

Go supports concurrency using *goroutines* (threads) and *channels*. 

#### Summary of quirks
- Entry point to a program *must* be a function called `main` inside `package main`
- doesn't have exceptions, so you may need to define functions with multiple return values (one for happy path, one for error)
- will not compile if you import a package but don't use it
- will not compile if you have unused variables
###### Strings
- are just *read-only slices of bytes* with a bit of extra syntactic support from the language.
- string are immutable, any conversion to type string creates a copy. 
- indexing a string yields its bytes, not its characters: a string is just a bunch of bytes
- must use double quotes or back ticks (single quotes are type `rune`)
###### Arrays
- rigid, rarely use.
- we must declare the type of elements in the array 
- arrays can only hold values of the same type and are ==*fixed length*==
- slices are abstractions over top of arrays that can be of varied length
###### Slices
[Usage and Internals 📖](https://go.dev/blog/slices-intro) 
- A slice is a data structure describing a contiguous section of an array stored separately from the slice variable itself. A slice is not an array. *A slice describes a piece of an array.*
- slices are a *window* into an array (starting pointer points to an element in an array)
	- mutations to the slice ==will mutate the underlying array== (shallow copy)
	- *length* of a slice is the number of elements in it 
	- *capacity* is the max number of elements a slice can hold, based on the length of underlying array 
	- the length of a slice can increase *up to its capacity* (aka the length of the initial array)
- ==capacity is fixed==.
- ==`append` returns a new slice==, hence allows slices to be dynamic
	- it does not mutate initial slice
	- if appending to a slice and the underlying array is full, it will create a new larger array (2x algorithm) and copy the values over
		- this is how dynamic arrays work in Python and JavaScript
- `copy` creates a deep copy of a slice/array at a new memory address.

###### Maps (dictionaries)
[💻 See code](Go%20Lang.md#Maps%20in%20code)
- mutable key value pairs
- has *length* property
- grow dynamically, but we can explicitly set an initial size to help with performance
- we can remove a value based on its key using `delete(<map>, <key>)`
- NOT sorted, will not guarantee order 
###### Loops and Conditionals
[💻 See code](Go%20Lang.md#For%20loops%20in%20code)
- use `for` keyword to create both for loops and while loops 
- arguments passed to functions need to have types declared
- drop `()` after `for`, `if`, `else if ` blocks
- you can [initialize a variable in an if statement ](./Go%20Lang%20Snippets.md#Initialized%20If)right before a condition is evaluated. These are scoped to the if/else if/else block only. 


###### Operations
- arithmetic operations, including comparison operations, must be done between  ==same type==
	- the return value will be the same type as the operands
- Comparison operators
	- numeric types must be of the same subtype (e.g. floats and ints)

###### Functions
- first class functions (can be passed as a value and assigned to variables)
- must state types for parameters AND each return value
- ==can return multiple return values==
- 🔑 Go passes *copies* of values as arguments to a function
	- If we want to mutate a struct's property, we need to pass a pointer to our value

###### Runes
- Unicode “Code point” is a bit of a mouthful, so Go introduces a shorter term for the concept: rune. 
- rune is an alias for the type `int32`, so programs can be clear when an integer value represents a code point.


#### Compiling
Compile a `go` file using `go build <filename>` into an exe file and then execute it from the terminal
```bash
$ go build tutorial.go
$ ./tutorial # for linux, tutorial.exe for windows 
> hello world
```

Compile and run a go file using `go run`
```
$ go run tutorial.go
> hello world
```

### Default values
Variables declared without an initial value are set to their zero values:
- 0 for all `int` types,
- 0.0 for `float`s,
- `false` for `bool`,
- `""` for `string`,
- `nil` for `interface`, `slice`, channels, `map`, `pointer` and `function`.

### Variable Declarations
We can *explicitly* declare a variable with a defined type or we can also let `go` *implicitly* guess the type when it's declared.

```go
package main
import "fmt"

const ( // untyped constant declarations are inferred
	Pi = 3.14
	Unit = "radian"
	No = !Yes // can be declared before Yes
	Yes = true
)

const ( // typed constants
	pi, height float32 = 3.14, 5.75 // must be same type to declare on same line
	a uint8 = 256 // overflows
	b = uint8(255) + uint8(1) // overflows
	c = uint8(-1) // cannot bind -1 to unsigned int
	)

// PURE variable declaration
var name = "Audry" // variable types can be inferred too
var ( // initialized to type's default value
	address string // ""
	age int  // 0
	x, y float32 = 1.0, 100.0
)

func main()  { // each local var must be used once or else error
	x, age = float32(age), int(x) // cannot reassign variables of different types without explict conv 
  	lang, year := "Go language", 2007
	year, createdBy := 2009, "Google Research" // at least one var must be a newly declared to use walrus
	_, _ , _ = year, lang, createdBy // avoid compilation error
  
}
```
When variables are declared but not assigned a value, they will be assigned their [default value](Go%20Lang.md#Types%20default%20values)

## Examples
Print formatting from `fmt` module
```go
 func main () {
	 num:= 10
	 num2:= 2.82
	 fmt.Printf("num 1 is %T", num1) // 10,
	 fmt.Printf("%f", num2) // 2.82 floats
	 fmt.Printf("%s", "tim") // tim
	 fmt.Printf("%q", "string") // "string" , literal quotes
 }
```

User input using the `os` and `bufio` packages
```js
package main
import (
	"fmt"
	"bufio"
	"os"
)

 func main () {
	 scanner:= bufio.NewScanner(os.Stdin) // create a new scanner object
	fmt.Println("Year born: ")
	 scanner.Scan() // scans for user text

	// convert scanned text from string to int
	// parse int using base 10, size 64 
	// return input and _ palceholder for "err"
	 input, _ := strconv.ParseInt(scanner.Text(), 10, 64)
	 fmt.Printf("You will be %q years old at end of 2022", 2022 - input)
 }
```

###### Arrays in code
```go
//initializes an array of strings (with their default values) with a max length of 5 elements
var arr [5]string // [     ] default value is empty strings
var arr [5]int // [0 0 0 0 0] default is 0
arr := make([5]string)

// declare and initialize array with values
arr := [5]string{"a", "b", "c", "d", "e"} // [a b c d e]
arr := [5]int{1, 2, 3, 4, 5} // [1 2 3 4 5]

len(arr) // 5
```

###### Slices in Code
[💬 Description](.md#Slices%20)

###### Strings in code
[💬 Description](.md#Strings%20)



###### For loops in code
 [💬For loops](Go%20Lang.md#Loops%20and%20Conditionals)
Two ways to loop through a slice/arrays/maps using full `for` syntax and also using `range` function
```go
func main() {
	var a []int = []int{1, 2, 3, 4, 5}

	// range shortcut instead of indexing
	for i, element := range a {
		fmt.Printf("index: %d, element: %d \n", i, element)

		// regular for loop and indexing
		for j := i + 1; j < len(a); j++ {
			element2 := a[j] /
			fmt.Printf("j: %d, element2: %d\n", j, element2)
		}
	}
}
```
- Iteration over maps are *unordered*

###### Maps in code
 [💬 Description](Go%20Lang.md#Maps%20dictionaries)

Creating maps and deleting keys. You *can* delete keys that don't exist, go won't complain
```go
// full syntax
var mp map[string]int = map[string]int{
	"apple":  5,
	"pear":   6,
}
// short syntax
mp := make(map[string]int) // map[]
mp["apple"] = 5
mp["pear"] = 6
mp["mango"] = 0

delete(mp, "kiwi") // no error, no return value
```

⚠️ If a key does *NOT* exist, go returns `0` by default. This doesn't differentiate from a key that *does* exist and has value of 0. You *must* check the second variable's boolean value to confirm whether or not the key exists. 
```go
val, exists := mp["apple"] // 5 true
val, exists := mp["mango"] // 0 true!!!

if _, exists := mp["mango"] {
	// do something if exists
}
```

Loop over key values
```go
for k, v := range mp {
	// ...
}
```

###### [Functions](Go%20Lang.md#Functions) - Named return values
```go
func add(x, y int) (z1, z2 int) {
	defer fmt.Println("deferred until return statement")
	z1 = x + y
	z2 = x - y
	fmt.Println("more code")
	return
}
func main() {
	ans1, ans2 := add(4, 6)
	fmt.Println(ans1, ans2)
}
```

###### [Functions](Go%20Lang.md#Functions) - multiple return values
```js
// must specify type for each return value
func add(x, y int) (int, int) {
	return x + y, x - y
}
func main() {
	ans1, ans2 := add(4, 6)
	fmt.Println(ans1, ans2) // 10 -2
}
```

###### [Functions](Go%20Lang.md#Functions) - passing functions as arguments
```js
// multiply takes a named multiplier function and an interger x as arguments
// Note: we must specifcy that multiplierFunc is a func(int) type that takes an int and returns an int
func multiply(multiplierFunc func(int) int, x int) {
	fmt.Println(multiplierFunc(x))
}
func main() {
	multiplyBy7 := func(x int) int {
		return x * 7
	}
	multiply(multiplyBy7, 2) //14
	multiply(multiplyBy7, 7) // 49
}
```

## Tips
download a go package from a specific branch on github 
- After Go 1.11, you can require a specific branch instead of letting `go get` default to latest release
```zsh
go get github.com/{package_url}@{branch_name}
# workaround if branch name has "/" in it 
go get github.com/teamookla/common-go@{commit_hash_of_branch}
```
