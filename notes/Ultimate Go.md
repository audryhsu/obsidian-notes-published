---
tags:
  - golang
  - ardanlabs
aliases: 
publish: true
---

To write better, you must read a lot of good and bad code. Programming is majority taught by writing before reading. 

Go is meant to be a language that can fully leverage all of the hardware. <mark style="background: #ABF7F7A6;">The machine is our mental model and platform. Core mission of go is to code in a way that you understand what the hardware is doing.</mark>

Go strikes balance of programmer productivity (good enough) and performance (fully leverage hardware available to us.)

> "Make it correct, make it clear, make it concise, make it fast. In that order."
> - Wes Dyer

🐛A bug is introduced every 20 lines of code. The more lines of code you write and don't need, the more likely you are introducing bugs and untested code. 
Let's have the minimal amount of code we need. 

# design guidelines
*Integrity* (reliability) is #1.
- at a micro level - integrity is being accurate, consistent, and efficient in every allocation, read, and write of memory
- cost of integrity is some performance.
- macro level - every problem is a data transformation problem.
- Bill: Go is a data-oriented language
> Failure is expected, so design systems that help you identify failure.
> "Product excellence is the different between something that only works under certain conditions vs one that only breaks under certain conditions."

*Maintaining our mental model of our code at all times is paramount.*
- Layers of abstractions hide the cost of our code because it's so abstracted we don't understand what it's doing. 
- we should be able to reasonably predict how the code is going to run. If we can't, then this weakens our mental model. 

*Code Readability*
- code must be readable by us AND the team
- an average developer should have a solid mental model of the code and know how to debug it
- on a team, above average developers are responsible for making sure the writing code in line with the team
- how can we bring the below average developers to average?
- **don't write code that is easiest to write, write code that is easiest to understand****

*Simplicity*
- Simplicity and readability are at tension.
- **simplicity is hiding complexity without losing readability**
- simplicity is only achieved through refactoring
- refactoring is what you do every day, not something you leave at the end.

# memory and data semantics
Type safety gives us integrity. We love it.
- type provides the amount of memory and what that memory represents to the complier
- the memory tells us how much 
Built in variable types
- `float64` tells us both what it represents and how much memory (8 bytes)
- 💡 this is an example of how Go keeps the programmer's mental model close to the platform (the hardware)

Go prefers conversion over casting
- `int32(10)`
- safer to allocate new memory of 32 bytes and move the value 

Structs are user-defined types, or a composite type (made up of other built in types). 

why are type restrictions necessary? This could cause bugs! 
```go
var a int
var b uint
// if the compiler allowed this, there would be  DATA loss because signed ints have a byte dedicated to the +/- 
a = b
```

## Pointers
Note: every goroutine gets it's own stack of 2kb
- every function is a go routine, with it's own isolated slice of the stack (a frame)
Value semantics
- value semantics refers to when a function works on it's **own copy of some data**
- when we enter a new function, Go is entering a new frame of the stack memory
- the function input is passed as a function parameters, and it's a *copy* of some data to be transformed
- this is safest way to work with data when being modified (data locality)

What's the cost of value semantics?
- less efficient to make a bunch of copies of the data, give it out to  4 functions to modify, and then have to combine it back again
- if we shared the data to 4 functions, we're being more efficient, but also side effects happening 

Pointer semantics (pass a pointer to a variable)
- Go can now access memory outside the function's frame of the stack
- anytime we modify data outside of the current stack frame, we're causing a *side effect*

Go uses factory functions to initialize structs
- What happens if you have a factory function that creates a pointer to a value and returns it?
- **escape analysis** happens during static code analysis
- tells you where values need to be constructed -- stack or the heap

```go
func main () {
	u := createUser()
}
func createUser() *user {
	u := User{ name: "audry"}
	return &u
}
```
- Go's escape analysis sees that `createUser` needs to share the memory address of `u` up the stack when the goroutine returns to the `main` stackframe. 
- how can it share memory that was created on the `createUser` stack frame, if that frame gets popped off the stack after the function returns?
- the escape analysis actually creates `u` **on the heap**, and then `main` gets a copy of `u`, which is a pointer to the data on the heap!
- that ALSO means that the garbage collector will be responsible for `u` from now on
- Bill mentions that this is pretty novel.
![Drawing_2024-05-14 09.46.42.excalidraw.svg](../images/Drawing_2024-05-14%2009.46.42.excalidraw.svg.md)
%%[🖋 Edit in Excalidraw](../images/Drawing_2024-05-14%2009.46.42.svg)%%


Guidelines for readability
- Use value construction for variables
- on the return line, then you can return the `&u` so readers know you are sharing a pointer up and down the call stack
- this helps readers understand the cost of your code (constructing on the heap)

we can see this escape analysis during profiling using the go complier flag
```
go build -gcflags -m=2
```

## garbage collector
- pacer figures out when the collector should run before we run out of heap space, without running too often

> [!NOTE] 🧠 mental model of garbage collector
> - if 3 MB of heap is in use, Go will create a 3 MB of heap gap (2x) == 6 MB total heap space
> - every sweep, the GC will recalibrate the heap gap
> - the faster the program consumes the heap gap, the more stress there is on the heap, the more often the GC has to run

GC algorithm goal is to reduce "stop the world" time to < microseconds
- GC has to scan the heap and block go routines from writes
- sometimes the GC needs to use more OS power to finish the job, might throttle a little bit
Be sympathetic to hardware
- try to reduce the number times garbage collection happens, reduces stop the world and reduces the amount of MB of heap to scan, and cut latency

### constants
skipped

# data structures
slices are the most important data structure. To understand why, we must understand mechanical sympathies and arrays.
*we recognize the hardware is the platform.*
## arrays
### mechanical sympathy
Case study: Why is row traversal 2x efficient compared to column traversal and linked list traversal? 
- row traversal is predictable what the next block of memory is
- column traversal spans OS pages, super inefficient
- linked list is better than column, but the memory isn't contiguous!

💡 If we can create predictable data access patterns, we can help the CPU cache prefetcher. 
- slices and arrays allocate contiguous memory
- maps are also try to do the same! 
![Pasted image 20240514103042.png](../images/Pasted%20image%2020240514103042.png)
>CPU: 4 physical cores, each core hosts 2 threads
- Each thread accesses memory from closest (L1) to farthest (main memory) with data access latency increasing by orders of magnitude 
![Drawing_2024-05-14 10.33.44.excalidraw.svg](../images/Drawing_2024-05-14%2010.33.44.excalidraw.svg.md)
%%[🖋 Edit in Excalidraw](../images/Drawing_2024-05-14%2010.33.44.svg)%%

### array semantics

Strings are internally by a StringHeader. Data points to underlying array of read-only bytes
```go
type StringHeader struct {
    Data uintptr
    Len  int
}
```
