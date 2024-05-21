---
creation date: 2022-03-20 08:02
aliases: 
tags: 🖥️
---
---
A pointer is a memory address , or the location to find actual value. Pointers are important because **Go passes copies of arguments to a function by default for all data types**, including slices and arrays! Pointers allow you to pass references to values.

#### Pointers and Dereference Operator (& and \*)
🧠 A pointer is the *directions* to the house vs dereferencing a pointer is being *at* a house. 
- `&x` --> 🗺️ "give me the memory address, or a pointer, to the value assigned to `x`"
	- `&` is called the address operator
- `*x` --> 🏠 "follow the pointer and give me the value that the pointer points to"

> [!warning]  `*` means two different things depending on the context:
> ```go
> func main() {
> 	x := 10
> 	p := &x
> 	// here *p means dereference operator, aka get the value stored at memory location p
> 	fmt.Println((*p) + 1 == 11) // true
> 	fmt.Println(addOne(p))
> }
> 
> // *int here means p is pointer TYPE
> func addOne(p *int) int {
> 	return *p + 1
> }
> ```
> 

#### When to use Pointers vs Values
Passing values is a good way to *make data immutable* (functions won't be able to change the variable outside of the function scope). However, passing *pointers is more memory efficient*.

**If you aren't sure, use a pointer.** 
- A local variable assignment
- Field in a structure
- Return value from a function
- Parameters to a function
- The receiver of a method

Use a pointer receiver when you want to:
- change the state of the receiver in a method (pass by reference)
- optimize the struct method (copy pointer instead of entire struct)

Use value receivers when:
- don't need to modify the receiver value (pass by value)
- concurrency safe (don't know what this means --> won't have race conditions?)

# Examples
###### Pointer Basics
We can change the value of x by dereferencing the pointer. 
```go
func main() {
	x := 7
	y := &x // y is equal to the pointer of x, aka memory location of "7"
	fmt.Println(x, y) //7 0xc0000b8000
	*y = 8 // dereference the pointer (access the block) and reassign value
	fmt.Println(x, y) // 8 0xc0000b8000
}
```

Pass by reference vs pass by value
```go
i := 1
func zeroval(ival int) {
    ival = 0
}
func zeroptr(iptr *int) {
    *iptr = 0
}
zeroval(i) // 1
zeroptr(&i) // 0!
```
- `zeroptr` has an `*int` parameter: takes an `int` pointer. `*iptr` dereferences the pointer from its memory address to the current value at that address. 
- Assigning a value to a dereferenced pointer changes the value at the referenced address.

###### Struct methods
```go
type Student struct {
	name   string
	grades []int
	age    int
}

// ❌ this won't mutate the struct, because we need a POINTER to the student struct to mutate its properties
func (s Student) setAge(age int) {
	// creates a LOCAL copy of student and reassigns the age
	s.age = age
}

// ✅ takes a pointer to value of type *Student
func (s *Student) setAgePoint(age int) {
	s.age = age
}

func main() {
	s1 := Student{"Audry", []int{90, 80, 100}, 27}
	s1.setAgePoint(22)
	fmt.Println(s1.age) // 22, mutated the struct!
}
	
```
###### Pointers to Slices
```go
func main() {
s2 := [][]int{{1, 2, 3}, {4, 5, 6}, {7, 8, 9}}
operateOnNestedSlice(&s2)
fmt.Printf("s2 after: %v\n", s2) // [[1 2 3] [4 5 6] [7 8 9] [100 101]]

}

func operateOnNestedSlice(p *[][]int) {
fmt.Println("len: ", len(*p)) // len: 3

// dereference first, then index into slice
fmt.Println("first slice:", (*p)[0]) // [1,2,3]
fmt.Println("len of first slice:", len((*p)[0])) // 3
fmt.Println("Index into nested slice:", (*p)[1][1]) // 5

// mutate slice using pointers
*p = append(*p, []int{100})
// mutate nested slice using pointer to parent slice
(*p)[3] = append((*p)[3], 101)

}
```

---
Tags: [Programming](Programming.md) - [Go Lang](./Go%20Lang.md) 

Reference:https://go.dev/blog/slices-intro, https://bitfieldconsulting.com/golang/pointers

Related: 