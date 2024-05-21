---
creation date: 2022-03-22 08:26
aliases: 
tags: 🖥️
---

---
Defer allows you to call a function that will be deferred until the end *before* the function returns. 

### Benefits of Deferred Function Feature
- commonly used for cleanup purposes (e.g. closing a file), and is used in places where `finally` is used in other language
	- This helps us not forget to close a file after we're done (risk of memory leak) and allows us to call close near where we initiated the open.
- required for [panic and recover mechanism](Go%20Lang%20Defer,%20Panic,%20Recover.md#Panic%20and%20Recover) 
- cleaner & robust code 
### Rules
1. Deferred function's arguments are evaluated *when defer statement is evaluated*
```go
func a() {
    i := 0
    defer fmt.Println(i)
    i++
    return
}
// "0"
```
1. Deferred function calls are executed in LIFO order *after the surrounding function returns or enters the exiting phase (panic)*
	1. When `defer` statement is executed, deferred function is added to deferred call **stack** maintained by caller goroutine.
```go
func b() {
    for i := 0; i < 4; i++ {
        defer fmt.Print(i)
    }
}
// "3210"
```
1. Deferred functions can read and assign to returning function's *named return values*
	1. This feature allows defer to modityf the error return value of a function
```
func c() (i int) {
    defer func() { i++ }()
    return 1
}
// 2
```


###### Timing of variable evaluation in deferred functions 
```go
package main

import "fmt"

func main() {
	func() {
		for i := 0; i < 3; i++ {
			defer fmt.Println("a: ", i) // i evaluates to i at time of defer statement executed (each loop)
		} 
	}()
	fmt.Println("---")

	func() {
		for i := 0; i < 3; i++ {
			defer func() {
				fmt.Println("b:", i) // i evaluated when anon func call is executed (value of i is already 3)
			}()
		}
	}()

}
/*
1st anon iife executes
	expression within for loop added to defer queue
		queued a: 0 added to defer queue
		queued a: 1..
		queued a: 2..
	exiting 1st anon function --> execute deferred expressions in reverse order 
"---"	
2nd anon iffe executes
	for loop runs and pushes anon func to deferred function queue
		queue b: 3
		queue b: 3
		queue b: 3
	exiting iife --> execute deferred functions. i evaluates to 3, part of closure
*/
```
### Panic and Recover
`panic` is like throwing an exception while `recover` is like catch
- Panicking causes a function to immediately return and *enter exiting phase*
	- deferred functions in queue are executed when exiting call stack (including any deferred `recover` functions)
- Call `recover()` function in a deferred function call to remove a live panic in current goroutine and enter normal calm status
	- return value by `recover` function is the value of the consumed `panic` function call
- If panicking goroutine exits without being recovered, it reaches the top of the goroutine's call stack and terminates the program. 

> [!NOTE] Panicking and order of deferred function calls
> 
> ```
> func main() {
> 	defer func() { // 5
> 		fmt.Println("runs last and exits normally")
> 		}()
> 
> 	defer func() { // 4 - recovers panic
> 		v:= recover()
> 		fmt.Println("recovered returns: ", v)
> 	}()
> 
> 	defer fmt.Println("runs before deferred recover") // 3
> 	fmt.Println("hi!") // 1
> 	panic("bye!") // 2 --> enter exiting status
> 	fmt.Println("unreachable")	
> }
> ```
> 


# Examples
Defer Recover and Panic
https://goplay.tools/snippet/_Zu057mxSK-


Deferring `closeFile`
```go
import (
	"fmt"
	"os"
)
func main() {

	// Immediately after getting a file object with
	// `createFile`, we defer the closing of that file
	// with `closeFile`. This will be executed at the end
	// of the enclosing function (`main`), after
	// `writeFile` has finished.
	f := createFile("/tmp/defer.txt")
	defer closeFile(f)
	writeFile(f)
}
func createFile(p string) *os.File {
	fmt.Println("creating")
	f, err := os.Create(p)
	if err != nil {
		panic(err)
	}
	return f
}

func writeFile(f *os.File) {
	fmt.Println("writing")
	fmt.Fprintln(f, "data")

}
func closeFile(f *os.File) {
	fmt.Println("closing")
	err := f.Close()
	// It's important to check for errors when closing a
	// file, even in a deferred function.
	if err != nil {
		fmt.Fprintf(os.Stderr, "error: %v\n", err)
		os.Exit(1)
	}
}
```



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: https://go.dev/blog/codelab-share

Related: 