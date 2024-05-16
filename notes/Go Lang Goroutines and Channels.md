---
tags: golang, concurrency
publish: true
aliases: 
cssclass: 
---

To take advantage of the speed increase that multiple cores provide, programs need to be able to split into multiple streams of code.
- Goroutines solve the challenge of setting up and running concurrent code in a program. 
- Channels solve the difficult of safely communicating between the code running concurrently.

Goroutines are maintained and ==scheduled by the language runtime instead of the OS, which makes it more efficient== than threads due to less memory consumption and context switching. 
### Goroutines
Goroutine is a special type of function that can run while other goroutines are also running. Each Go program starts with one main goroutine.
#### Synchronizing goroutines
Two main ways to synch are using:
1. [Channels](Go%20Lang%20Goroutines%20and%20Channels.md#channels) - most popular and unique
2. [WaitGroup](Go%20Lang%20Goroutines%20and%20Channels.md#waitgroup) - data structure to facilitate waiting for a *known number* of go routines to be done before the outer function returns.
	1. a `Waitgroup` is a data structure to essentially count threads until they're all done
	2. typical to use an [Immediately invoked function expressions (IIFE)](../Immediately%20invoked%20function%20expressions%20(IIFE).md) to create a [closure](../Closures.md) around spawned goroutines to share waitgroup state

###### Goroutine Schedule
![Goroutines and Channels 2022-11-07 09.48.03.excalidraw](../images/Goroutines%20and%20Channels%202022-11-07%2009.48.03.svg)


##### WaitGroup
Methods:
	1. `Add` - registers the number of new tasks
	2. `Done` - notify that task is finished
	3. `Wait` - makes caller goroutine blocking until all registered tasks are finished (e.g. make main wait to exit program until all goroutines are done)
	
To use goroutines, you need:
- define goroutine functions that take a  `waitgroup` variable & call `defer waitgroup.Done()`
- declare a `waitgroup` variable from `sync.WaitGroup` from the `sync` package in main code
	- use `waitgroup.Add()` and pass in the number of goroutines to the waitgroup
	- invoke functions as goroutines by appending the function with `go` keyword and passing in the `waitgroup` variable
- call `waitgroup.Wait()` to ensure that main waits for the goroutines to finish before exiting

Avoid starting too many to avoid resource starvation, since there are switching costs between goroutines.
- In some cases, it may be faster to run fewer functions concurrently
- Rule of thumb is to run x concurrent functions given x cores

### Channels
- [Playground example](https://goplay.tools/snippet/Wo48oGKxsdR)
- `chan` data structure is similar to slices or maps, and must be declared to take a certain type in the channel
- many functions can read from the same channel, however only one goroutine has access to the value at any given time by design to prevent *data races*

![Drawing_2023-06-27-go-channels.excalidraw.svg](../images/Drawing_2023-06-27-go-channels.excalidraw.svg.md)

> [!Warning]
> - Unclosed channels can cause [memory leaks](../Memory%20Leaks.md).
> - deadlocks when two parts of program are waiting on each other to do something (writing to channel waits for reads and vice versa) can cause blocking
> - channels are blocking when being written or read from
> 
> Less error prone if explictly define which functions read and which ones write to a channel
> 

# Examples
WaitGroups
```go
package main

import (
	"log" // synchronized print statements?
	"sync"
	"time"
)

var wg sync.WaitGroup

func myFunc() {
	time.Sleep(1500 * time.Millisecond)
	for i := 0; i < 3; i++ {
		log.Println("my func: ", i)
	}
	wg.Done() // equivalent to wg.Add(-1)
}

func anotherFunc() {
	time.Sleep(500 * time.Millisecond)
	for i := 4; i < 7; i++ {
		log.Println("another func", i)
	}
	wg.Done()
}

func main() {
	wg.Add(2)
	go myFunc()
	go anotherFunc()
	wg.Wait() // block until all tasks finished instead of using time delay
}

```
Reference: https://www.digitalocean.com/community/tutorials/how-to-run-multiple-functions-concurrently-in-go
[Gophercon - Understanding Channels](https://www.youtube.com/watch?v=KBZlN0izeiY)

