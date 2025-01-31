---
tags:
  - golang
  - ardanlabs
  - books
  - concurrency
  - ultimatego
aliases: 
publish: true
---
🗒️[Course Notes](https://github.com/ardanlabs/gotraining/blob/master/topics/courses/go/README.md)

To write better, you must read a lot of good and bad code. Programming is majority taught by writing before reading.

Go is meant to be a language that can fully leverage all of the hardware. <mark style="background: #ABF7F7A6;">The machine is our mental model and platform. Core mission of go is to code in a way that you understand what the hardware is doing.</mark>

Go strikes balance of programmer productivity (good enough) and performance (fully leverage hardware available to us.)

> "Make it correct, make it clear, make it concise, make it fast. In that order."
> - Wes Dyer

🐛A bug is introduced every 20 lines of code. The more lines of code you write and don't need, the more likely you are introducing bugs and untested code. 
Let's have the minimal amount of code we need. 

# Design guidelines
🗒️ [Design guidelines](https://github.com/ardanlabs/gotraining/tree/master/topics/go#design-guidelines)
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


> [!NOTE] Code Review Guidelines
> - structs are user defined types - use pointer semantics
> - array and ints are built in types
> - For built-in (array, ints) and reference  (slices, channels, and maps), **use value semantics/copies to move data around**
> 	- **guarantees that each function is operating on its own copy**
> [Consistency guidelines on value vs pointer semantics](Ultimate%20Go.md#Consistency%20guidelines%20on%20value%20vs%20pointer%20semantics)


# Language Mechanics
## memory and data semantics
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

### Value vs Pointer Semantics
- every function is a go routine, with it's own isolated slice of the stack (a frame 2kb)
<mark style="background: #FFB8EBA6;">Value semantics</mark>
<mark style="background: #FFB8EBA6;">- value semantics refers to when a function works on it's **own copy of some data**</mark>
- when we enter a new function, <mark style="background: #FFB8EBA6;">Go is entering a new frame of the stack memory</mark>
- the function input is passed as a function parameters, and it's a *copy* of some data to be transformed
- <mark style="background: #FFB8EBA6;">this is safest way to work with data</mark> when being modified (data locality)

What's the cost of value semantics?
- less efficient to make a bunch of copies of the data, give it out to  4 functions to modify, and then have to combine it back again
- if we shared the data to 4 functions, we're being more efficient, but also side effects happening

<mark style="background: #ADCCFFA6;">Pointer semantics (pass a pointer to a variable)</mark>
- Go can now <mark style="background: #ADCCFFA6;">access memory outside the function's frame of the stack</mark>
- anytime we modify data outside of the current stack frame, we're causing <mark style="background: #ADCCFFA6;">a *side effect*</mark>

#### Escape analysis
```go
func main () {
	u := createUser()
}
func createUser() *user {
	u := User{ name: "audry"}
	return &u
}
```
<mark style="background: #BBFABBA6;">Go uses **escape analysis** happens during static code analysis to determine where values need to be constructed -- stack or the heap</mark>
- Go uses factory functions to initialize structs
- What happens if you have a factory function that creates a pointer to a value and returns it?
- Go's escape analysis sees that `createUser` needs to share the memory address of `u` up the stack when the goroutine returns to the `main` stackframe. 
- how can it share memory that was created on the `createUser` stack frame, if that frame gets popped off the stack after the function returns?
- the escape analysis actually creates `u` **on the heap**, and then `main` gets a copy of `u`, which is a pointer to the data on the heap!
- that ALSO means that the garbage collector will be responsible for `u` from now on
- Bill mentions that this is pretty novel.

![Drawing_2024-05-14 09.46.42.excalidraw](../images/Drawing_2024-05-14%2009.46.42.svg)

Guidelines for readability
- Use value construction for variables
- on the return line, then you can return the `&u` so readers know you are sharing a pointer up and down the call stack
- this helps readers understand the cost of your code (constructing on the heap)

we can see this escape analysis during profiling using the go complier flag
```
go build -gcflags -m=2
```
#### Stack vs Heap

_"The stack is for data that needs to persist only for the lifetime of the function that constructs it, and is reclaimed without any cost when the function exits. The heap is for data that needs to persist after the function that constructs it exits, and is reclaimed by a sometimes costly garbage collection." - Ayan George

### garbage collector
[Garbage Collection Semantics Part I](https://www.ardanlabs.com/blog/2018/12/garbage-collection-in-go-part1-semantics.html) - William Kennedy
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

### Constants
Constants are a way to create a named identifier whose value can never change.
* Constants are not variables.
* They exist only at compilation.
* Untyped constants can be implicitly converted where typed constants and variables can't.
* Think of untyped constants as having a Kind, not a Type.
* Learn about explicit and implicit conversions.
* See the power of constants and their use in the standard library.

## Data structures
slices are the most important data structure. To understand why, we must understand mechanical sympathies and arrays.
*we recognize the hardware is the platform.*
### arrays
🗒️ [Arrays](https://github.com/ardanlabs/gotraining/tree/master/topics/go/language/arrays#arrays)
##### Create mechanical sympathy with CPU caches
- [CPU Caches and Why You Care (18:50-20:30)](https://youtu.be/WDIkqP4JbkE?t=1129) - Scott Meyers  
- [CPU Caches and Why You Care (44:36-45:40)](https://youtu.be/WDIkqP4JbkE?t=2676) - Scott Meyers   
- [Performance Through Cache-Friendliness (4:25-5:48)](https://youtu.be/jEG4Qyo_4Bc?t=266) - Damian Gryski  

Case study: Why is row traversal 2x efficient compared to column traversal and linked list traversal? 
- row traversal is predictable what the next block of memory is
- column traversal spans OS pages, super inefficient
- linked list is better than column, but the memory isn't contiguous!
#### CPU Cache Notes
* CPU caches works by caching main memory on cache lines.
* Cache lines today are either 32 or 64 bytes wide depending on the hardware.
* Cores do not access main memory directly. They tend to only have access their local caches.
* Both data and instructions are stored in the caches.
* Cache lines are shuffled down L1->L2->L3 as new cache lines need to be stored in the caches.
* Hardware likes to traverse data and instructions linearly along cache lines.
* Main memory is built on relatively fast cheap memory. Caches are built on very fast expensive memory.

* Access to main memory is incredibly slow, we need the cache.
	* Accessing one byte from main memory will cause an entire cache line to be read and cached.
	* Writes to one byte in a cache line requires the entire cache line to be written.

* Small = Fast
	* Compact, well localized code that fits in cache is fastest.
	* Compact data structures that fit in cache are fastest.
	* Traversals touching only cached data is the fastest.

* Predictable access patterns matter.
	* Whenever it is practical, you want to employ a linear array traversal.
	* Provide regular patterns of memory access.
	* Hardware can make better predictions about required memory.

* Cache misses can result in TLB cache misses as well.
	* Cache of translations of a virtual address to a physical address.
	* Waiting on the OS to tell us where the memory is.### Cache Hierarchies
💡 If we can create predictable data access patterns, we can help the CPU cache prefetcher. 
- slices and arrays allocate contiguous memory
- maps are also try to do the same! 
#### Cache hierarchy
This is a diagram showing the relationship of the cache hierarchy for the 4 Core i7-9xx processor. The caches in the diagram are not to scale. 
- This processor has four cores and each core has two hardware threads. 
- The hardware threads per core share the Level 1 caches. 
- The cores have individual Level 1 and Level 2 caches. All cores for all the processor share the L3 cache.
![Pasted image 20240514103042.png](../images/Pasted%20image%2020240514103042.png)
>CPU: 4 physical cores, each core hosts 2 threads
- Each thread accesses memory from closest (L1) to farthest (main memory) with data access latency increasing by orders of magnitude 
![Drawing_2024-05-14 10.33.44.excalidraw](../images/Drawing_2024-05-14%2010.33.44.svg)
%%[🖋 Edit in Excalidraw](../images/Drawing_2024-05-14%2010.33.44.svg)%%

#### array semantics

Strings are internally by a StringHeader. Data points to underlying array of read-only bytes
```go
type StringHeader struct {
    Data uintptr
    Len  int
}
```

Guidelines for using built-in types
- when using strings, use value semantics to pass the string and slices of string around
- may not realize it, but the array backing the string we use pointer semantics!

### slices
🗒️ [Slices](https://github.com/ardanlabs/gotraining/tree/master/topics/go/language/slices#slices---arrays-slices-and-maps)
A slice is ALWAYS a "3 word" data structure

![Ultimate Go 2024-09-09.10.53.05 slice](../Ultimate%20Go%202024-09-09.10.53.05%20slice.md)

https://goplay.tools/snippet/XRlMt15vuK7
- when you are indexing INTO a slice -- using pointer semantics
- when you are ranging over a COPY of a slice -- value semantics
see notes [Drawing_ultimate_go_notebook_chapter3](../Drawing_ultimate_go_notebook_chapter3.md)

#### appending to slices
![Pasted image 20240909112104.png](../images/Pasted%20image%2020240909112104.png)

Gotchas:
- be careful when changing `slice[i]` if the backing array is being shared
- using `append` has the same side effect, just hidden
	-  `append` always maintains a continguous backing array and could change the reference
- use the 3 [index slice](https://goplay.tools/snippet/Ntj3PrpYB-F) to set `slice[start:end:cap]` where `len == cap`. This will force any `appends` to create a new backing array.
- mitigates side effects when slices share same backing array
#### codepoints aka runes
In Go, strings are just bytes interpreted in UTF-8.

[UTF-8](UTF-8.md) character set is made of up bytes, code points, and characters.
- 1 - 4 bytes of data can represent a code point `int32` aka `rune` 
- code point can represent a characters like chinese

Code: https://goplay.tools/snippet/KiSQzmHSmW0

#### for range value vs pointer semantics
https://goplay.tools/snippet/8hRojCy02n-
### Maps
🗒️[Maps](https://github.com/ardanlabs/gotraining/blob/master/topics/go/language/maps/README.md)

What data types can used for map keys? 
- limited to data types that are used in conditional logic

Ranging over maps **does not guarantee order**
- we can sort the keys first, then range over the sorted keys to access the map 

Maps are reference types, pass COPIES of maps when moving it around the code (e.g. passing into functions)
- maps are just pointers anyways, so we're passing copies of pointers
- hence, we don't ever need a pointer to a pointer...
- reading and writing uses pointer semantics
- access a map is not concurrency safe multi-threaded code, we need to lock it up or else data races can occur

## Decoupling code from change
[Drawing_ultimate_go_decouple_ch4.svg](../Drawing_ultimate_go_decouple_ch4.svg)

## Methods
🗒️[Methods](https://github.com/ardanlabs/gotraining/blob/master/topics/go/language/methods/README.md) 

❓Should your API use value or pointer semantics?

Reasoning: If you modify or mutate the data, does it represent something new?
- eg. time - if we mutate 5:00pm to 5:05pm, represents a *new*  time, so returning a new copy would make sense
- read factory function to infer developer's intent on API's use of value vs pointer semantics
- eg `File` - API returns a *pointer* to `*File`, then API wants you to *share* references 

Readability guidelines:
1. Define the Type
2. Factory function to create struct
3. Methods

<mark style="background: #ADCCFFA6;">Exception: you need to use a pointer to represent concept of `null` or decoding/unmarshalling</mark>
- do NOT try to change from pointer semantics to value semantics. taking a copy of a value that a pointer is pointing to....just confusing and bug city

###### Consistency guidelines on value vs pointer semantics:
- Built-in types and struct fields use value semantics to move data around
- Reference types (slices, channels, maps, functions) - value semantics to move around data, but pointer/shared access for reading/writing
- Structs types - decide based on the data model

Bill: "Getters and setters are not an API. Changing state isn't enough for an API."

Using functions is one way to implement indirection/decoupling. Bill says though that double indirection will always create a memory allocation on the heap (gc).

## Interfaces
[🗒️Interfaces](https://github.com/ardanlabs/gotraining/blob/master/topics/go/language/interfaces/README.md) 
- Interfaces are reference types, don't share with a pointer.
### Interfaces are polymorphic
>_"Polymorphism means that you write a certain program and it behaves differently depending on the <mark style="background: #FF5582A6;">(concrete)</mark> data that it operates on." - Tom Kurtz (inventor of BASIC)_

<mark style="background: #FF5582A6;">Interfaces are valueless-- nothing concrete about it. We cannot read/write to it! </mark> 
![Ultimate Go 2024-09-14.12.08.39 interfaces are pointers](../Ultimate%20Go%202024-09-14.12.08.39%20interfaces%20are%20pointers.md)
![Pasted image 20240915143451.png](../images/Pasted%20image%2020240915143451.png)
Interfaces define *behavior*
❓ Why are interfaces considered polymoprhic?
- Behavior can be implemented by a concrete type via methods
- When concrete type implements methods of interface, values of the concrete type --> assigned to values of interface type
- Method calls against interface --> call into the equivalent method of  concrete type, which makes interfaces polymorphic 

Even though we can declare a variable with type interface:
```go
var r reader

// retrieve is NOT asking for a reader value--- they don't exist
// "I will accept any concrete data (value or pointer) that implements the full method set defined by reader"
func retrieve(r reader) error {}
```
- we're never really passing an interface around, we're passing the concrete type that the interface points to around

### Method sets
[Playground](https://goplay.tools/snippet/Slza1gK9NrO)
- Methods declared with a pointer receiver, only implement the interface with pointer values.

## Embedding
[🗒️Embedding](https://github.com/ardanlabs/gotraining/blob/master/topics/go/language/embedding/README.md) 
[Embedding in Go Lang](../Embedding%20in%20Go%20Lang.md)
- Embedding types allow us to <mark style="background: #ADCCFFA6;">share state or behavior between types</mark>.
- The inner type never loses its identity.
- This is **not inheritance**, not subtyping
- Through <mark style="background: #ADCCFFA6;">promotion, inner type fields and methods can be accessed through the outer type.</mark>
- The outer type can override the inner type's behavior.


## Exporting
[🗒️Exporting](https://github.com/ardanlabs/gotraining/blob/master/topics/go/language/exporting/README.md)

- Convention: Uppercase types and variables are exported
- Avoiding configuration: "public", "private", "protected"

# Software Design
## Composition
🗒️[Composition](https://github.com/ardanlabs/gotraining/blob/master/topics/go/design/composition/README.md)

_"Don't design with interfaces, discover them." - Rob Pike_
- Otherwise...you're guessing!
- this isn't [Object Oriented Programming (OOP)](../Object%20Oriented%20Programming%20(OOP).md) ...<mark style="background: #BBFABBA6;">Don't group types by a common DNA but by a common behavior.</mark>

> [!EXAMPLE]  [Grouping by DNA instead of behavior](https://go.dev/play/p/Dh_cCEz3o0N)
> 
> Bad smells:
> - `Animal type` is an abstraction of shared state e.g. `name`
> 	- we want to embed types into structs for *behavior*, not state
> - Animal doesn't really represent something new and unique...its `speak` method is a generalization, and isn't ever going to be called
> - Driver code doesn't create or soley use the Animal type...
> - Unused code is a big 🚩 -- thats just more things we have to write tests for and maintain.... pollution!
> Better code: [Grouping By Behavior](https://play.golang.org/p/wRpHBoPu79K)
>
> _"Duplication is far cheaper than the wrong abstraction. - Sandi Metz_
> - duplicating `name` state for a `Dog` and a `Cat` is ok! 

Guidelines for declaring types:
- Validate that a value of any type is created or used on its own.
- Embed types to *reuse existing behaviors* you need to satisfy.
- Question types that are an alias or abstraction for an existing type.
- Question types whose sole purpose is to share common state
- declared types should represent something new or unique
#### Bill's approach to API design

> [!Example] [Decoupling in design](https://github.com/ardanlabs/gotraining/blob/master/topics/go/design/composition/README.md#decoupling)
> 1. Draft with concrete types first -- discover the interfaces later!
> 2. Articulate the primitive problems and solve it with the primitive API layer first. Make it work! Once primitive API layer works, design lower level, then high level API
> 3. Decouple review - does API still work if one of the conrete types change?
> 4. Readability review
> 	1. can we simplify interfaces? do we need them all? 

#### Assertions
Compile time vs runtime
- We use type assertions to test that an assignment to an interface is legal when the compiler can't be certain at compile time.
- [Playground](https://go.dev/play/p/sNP3bMR1kc-)

Type assertions are also a cool way to let code override default behavior of a method
- Example: [io package](https://cs.opensource.google/go/go/+/refs/tags/go1.23.1:src/io/io.go;l=398)
![Pasted image 20240916090356.png](../images/Pasted%20image%2020240916090356.png)

#### Designing instead of discover interfaces creates pollution 
Smell tests:
- interface name is a noun -- interfaces describe *behavior* 
- factory function creating an interface...should usually be the concrete type
- Am i implementing the `server` (concrete) or am i asking other developers to implement it (interface)?
- interface exists only for tests

Interfaces cost $ by adding indirection (readability/more code) and memory allocation (to store the concrete type in the interface). Make sure it's worth it!
![Pasted image 20240916091851.png](../images/Pasted%20image%2020240916091851.png)

> [!NOTE] When to use an interface?
> - users of the API need to *provide the implementation*
> - APIs have *multiple implementations* that need to be maintained
> - parts of the APIs that can change have been identified and require *decoupling*
> 

#### Interfaces for mocking
Design the API for the user first, not the test
👉 lower level unexported APIs should focus on testability
- concrete data in, data out is most testable
- separate the data transformation part
👉 higher level exported APIs should focus on usability
### Error Handling 
- Use the default error value for static and simple formatted messages.
- Create and return error variables to help the caller identify specific errors.
- Create custom error types when the context of the error is more complex.
- Error Values in Go aren't special, they are just values like any other, and so you have the entire language at your disposal.

Error handling is about 3 things:
1. logging the error
2. not propogating the error any further
3. determining if the goroutine/program needs to be terminated
<mark style="background: #ADCCFFA6;">It's my job to provide enough context for the user to decided #3</mark>

Idiomatic to use <mark style="background: #ABF7F7A6;">error variables</mark> to provide a mechanic way to identify what specific error is being returned. 
```go
var ErrBadRequest = errors.New("Bad request")
var ErrPageMoved = errors.New("Page moved")
//...
if err != nil {
	switch err {
		case ErrBadRequest:
			//....
		case ErrPageMoved:
			//...
	}
}
```
- note the use of errors constructor function
- easy to use `switch` statement

Use the type of the error as context
- If more context for an error is needed, consider creating a custom concrete error type that implements `Error` interface.

See the `json` package `InvalidUnmarshalError` as an example
![Pasted image 20241001120333.png](../images/Pasted%20image%2020241001120333.png)

###### Use behavior as context
- one problem of using  type assertions checking for a *concrete error value* means the code is coupled to that concrete value. If concrete error value is changed, my code can break.
- Solution is to use type assertion to test if *behavior exists* to keep error handling decoupled
- A common method is to create an error interface with a method called `Temporary` that allows users to test if networking error is critical or if it can recover on its own
- ![Pasted image 20241130094257.png](../images/Pasted%20image%2020241130094257.png)
- code is checking if the concrete error type implements the `temporary` interface

> [!important]
> Always return the error interface as the return type for handling errors so that error handling is decoupled!!
> 
> 🐛🐞 Example of bug when you return a concrete error instead of error interface:
> [Playground](https://goplay.tools/snippet/jmXrNby0x8X)
###### Wrapping errors
[Wrapping errors](../Go%20Lang%20Error%20Handling.md#Wrapping%20errors) and passing it up the call stack is a useful pattern when the current or intermediate functions aren't equipped to handle the error (e.g. not allowed to log)
- [Why wrap errors?](../Go%20Lang%20Error%20Handling.md#to%20wrap%20or%20not%20to%20wrap?)To make sure we preserve and pass enough call stack and user context
- Once we get to a function that *can* handle the error, unwrap the error using `errors.Is()` or `errors.As()` to extract the underlying cause
	- `errors.Is()` checks for equality between error in the chain and target error  
	 - `errors.As` function behaves like a type assertion.
https://goplay.tools/snippet/9g9oKtGokFx

Exercises:
- https://goplay.tools/snippet/yKjkjXpMb64
- https://goplay.tools/snippet/t0wDoqXc04D
### Packaging
to watch
🗒️ [Package Oriented Design](https://github.com/ardanlabs/gotraining/blob/master/topics/go/design/packaging/README.md#package-oriented-design)
# Concurrency (Ch 6)
### Scheduler semantics
[OS Scheduler blog post](https://www.ardanlabs.com/blog/2018/08/scheduling-in-go-part1.html) by Bill Kennedy
![Pasted image 20241001130318.png](../images/Pasted%20image%2020241001130318.png)
<mark style="background: #FFB8EBA6;">The purpose of the operating system scheduler is to create the illusions that multiple pieces of work are being executed at the same time</mark>
- ❗only one thread can physically be executing at the same time

<mark style="background: #ADCCFFA6;">Thread</mark>: A path of execution that is scheduled and performed. Threads are responsible for the execution of instructions on the hardware.
	- OS threads are waiting to be scheduled onto a hardware thread
<mark style="background: #ADCCFFA6;">Thread States</mark>: A thread can be in one of three states: Running, Runnable, or Waiting. 
- Running means the thread is executing its assigned instructions on the hardware by having a G placed on the M. 
- Runnable means the thread wants time on the hardware to execute its assigned instructions and is sitting in a run queue. Waiting means the thread is waiting for something before it can resume its work.
- Waiting threads are not a concern of the scheduler.
<mark style="background: #ADCCFFA6;">Concurrency</mark>: This means undefined out of order execution. In other words, given a set of instructions that would be executed in the order provided, they are executed in a different undefined order, but all executed. The key is, the result of executing the full set of instructions in any undefined order produces the same result. I will say work can be done concurrently when the order the work is executed in doesn’t matter, as long as all the work is completed.
<mark style="background: #ADCCFFA6;">Parallelism:</mark> This means doing a lot of things at once. For this to be an option, I need the ability to physically execute two or more operating system threads at the same time on the hardware.
<mark style="background: #ADCCFFA6;">CPU Bound Work</mark>: This is work that does not cause the thread to naturally move into a waiting state. Calculating fibonacci numbers would be considered CPU-Bound work.
<mark style="background: #ADCCFFA6;">I/O Bound Work:</mark> This is work that does cause the thread to naturally move into a waiting state. Fetching data from different URLs would be considered I/O-Bound work.
<mark style="background: #ADCCFFA6;">Synchronization</mark> - two or more goroutines need to *access the same memory location* potentially at the same time
- bad synchronization causes data races!
- 🔨use read/write mutexes, atomics
<mark style="background: #ADCCFFA6;">Orchestration</mark> - two or more goroutines need to *signal* to each other
- lack of signaling breaks concurrency guarantees and that work will be completed  
- 🔨use [Go Lang Goroutines and Channels > WaitGroup](./Go%20Lang%20Goroutines%20and%20Channels.md#WaitGroup) and [channels](./Go%20Lang%20Goroutines%20and%20Channels.md#Channels) 

#### 🧠 Operating system scheduler mental model
![Drawing_2024-12-01.10.52.47 os scheduler period](../Drawing_2024-12-01.10.52.47%20os%20scheduler%20period.md)
- the scheduler period is a finite window of time
- if we try to equally schedule every runnable thread, we'll reach a point of diminishing returns where the hardware is spending more time switching threads than actually executing work
- this is known as Context Switch Cost 
- 1 - 2 microseconds is overhead to take one thread off and put a new thread on 
	- this is 1000-2000 nano seconds, or up to 24k set of instructions (12 instructions / nanosecond)
- to avoid this, we can set the `minimum time scheduled` to ensure that each thread has a reasonable amount of execution time
- to not adverse affect throughput, we need to balance the number of running threads, since increasing the schedule period means we could be waiting many seconds before a thread gets its turn again.

As a developer, I need to understand two types of workloads and how concurrency works for or against it:
- CPU bound workloads - work never moves to waiting state, which means it gets its full scheduled time
	- context switches/concurrency is **detrimental** for this type of work
	- the most efficient way to schedule CPU bound work is to have 1 os thread / 1 hardware thread bc context switching will extend the schedule period and affect throughput
	- you can increase throughput by adding hardware threads aka **parallellism**
- 
- I/O bound - makes a call to OS or over the network within it's scheduled time
	- context switching is beneficial in this case, because this work moves to a waiting state, we *want* the schedule to switch this thread out for another instead of staying idle
	- more difficult to determine sweet spot of threads where we have minimal number of threads in a runnable state waiting for their turn

> [!Tip]- Parallelism is for CPU bound work, Concurrency is for I/O bound work
> CPU bound work
>  - adding a list of numbers can be done in any order. If we use concurrency, we can split the list into k partitions and assign a thread to add those partitions up
> - we're wasting time context switching when we could have been adding, therefore concurrency is not helpful
> - parallelism (adding a hardware thread) lets us add k partitions at the same time to get to the sum much faster
>
> I/O bound work
> - run through a list of url fetches. If we use concurrency, each thread is responsible for fetching k urls
> - a thread might make a network call within the first 1ms of it's scheduled time, then go into `waiting` state
> - it's more efficient to switch threads than let the hardware thread be idle, therefore concurrency is beneficial!

<mark style="background: #FFB8EBA6;">OS schedulers are preemptive scheduler, which makes them unpredictable</mark>
- every time an event occurs on machine, a thread has to respond
- even keeping track of time on the machine clock is an event, so the OS schedule has to get that priority event on the hardware thread
- as a developer, we are responsible for synchronization and orchestration to provide guarantees that our code gets the correct results even if they are executed out of order 


These ~8 minutes from Scott Meyers is great to listen to here: CPU Caches and Why You Care 30:09-38:30

https://youtu.be/WDIkqP4JbkE?t=1809

### Go scheduler mechanics
[Go Scheduler blog](https://www.ardanlabs.com/blog/2018/08/scheduling-in-go-part2.html) post by B. Kennedy
![Drawing_2024-12-01.11.35.54 Go scheduler](../Drawing_2024-12-01.11.35.54%20Go%20scheduler.md)
- Go scheduler sits on top of OS scheduler, and is very similar in concept to minimal time slice, schedule period, and context switching
- cooperative scheduler - goroutines have to yield their time for the go scheduler to make a context switch 

✨ The magic of Go scheduler is that goroutines are context switched on and off the same OS thread + core. This saves the OS scheduler from having to context switch on/off OS threads onto hardware. Go has turned I/O blocing work into CPU bound work at the OS level.
- <mark style="background: #FFB8EBA6;">OS thread never moves into waiting state</mark>
-  OS thread context switches ~1000 nanoseconds vs Goroutine  ~200 nanoseconds
- Go scheduler uses less OS threads, but does more work on each thread to reduce load on OS and hardware!
![Pasted image 20241201124449.png](../images/Pasted%20image%2020241201124449.png)
![Pasted image 20241201124434.png](../images/Pasted%20image%2020241201124434.png)
- notice that the diagram for I/O bound work on OS threads vs Goroutines look exactly the same.
- the only difference is that T1 and T2 could run on a different core, whereas G1 and G2 run on the same thread+core
- 
### Goroutines
[Goroutines](https://github.com/ardanlabs/gotraining/blob/master/topics/go/concurrency/goroutines/README.md)

Tips:
- waitgroups - keep the `Add` and `Done` calls in the same line of sight to reduce bugs. Try not to pass WaitGroup as a function parameter or else it'll get lost
- Use the `-race` to instrument tests or binary builds
- There are no guarantees or predictability of when the scheduler will context switch, so keep synchronization tight
- mutexes are not a queue - doesn't guarantee order
- 


### Data races
[Data Races](https://github.com/ardanlabs/gotraining/blob/master/topics/go/concurrency/data_race/README.md) 
### Channels
[Channels](https://github.com/ardanlabs/gotraining/blob/master/topics/go/concurrency/channels/README.md)
### Concurrency Patterns
[Context](https://github.com/ardanlabs/gotraining/blob/master/topics/go/packages/context/README.md) | [Patterns](https://github.com/ardanlabs/gotraining/blob/master/topics/go/concurrency/patterns/README.md)

# Testing and Profiling

This material covers a good portion of the tooling that comes with go. Specifically we cover testing and benchmarking. We also cover profiling memory and the scheduler. Finally we learn how to read stack traces.
# Packages

This material covers the essential things you need to know about the standard library and some important third party packages. Along the way you also learn about the most commonly used packages.
