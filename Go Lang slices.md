---
tags: 
aliases: 
---

Slices are a struct with 3 pieces of information:
```go
type slice struct {
	// Size of used space in data
	len  int
	// Size of data
	cap  int
	// pointer to Underlying array
	data *[...]Type
}
```

Ways to initialize slices
```go
names := []string{"leto", "jessica", "paul"} 
checks := make([]bool, 10) // when you will be writing into specific indicies
var names []string // use with append & when element count is unknown
scores := make([]int, 0, 20) // specify initial capacity
```

create slice from existing array
```go
var x [5]int = [5]int{1,2,3,4,5} // array
var s []int = x[:] // copy entire array

// slice starting from index 1 up to NOT including 3
var s []int = x[1:3] // [2 3] 
```
- when a slice is initialized, an array is actually created under the hood and the slice is taken of the array

### Mutating slices
There are two ways to mutate a slice:
1. Pass a [pointer](./Go%20Lang%20Pointers.md) to slice to the mutating function, or  
2. Pass copy of the slice to a function, then reassign the original slice to the returned slice
[Playground code](https://goplay.tools/snippet/b79F77nboEl)

### Slicing does not copy data
Copying data using the `copy` function or appending to a slice that requires more capacity will reallocate data to new memory location.  
```go
func main() {
	src := []string{"a", "b", "c"}
	shallow := src[:]

	dst := make([]string, len(src))
	copy(dst, src)

	fmt.Printf("source slice: %[1]v, address: %[1]p\n", src)        //source slice: [a b c], address: 0xc000118180
	fmt.Printf("slice of slice: %[1]v, address: %[1]p\n", shallow)  // slice of slice: [a b c], address: 0xc000118180
	fmt.Printf("deep copy of slice: %[1]v, address: %[1]p \n", dst) // deep copy of slice: [a b c], address: 0xc0001181b0
}
```
### Sorting slices
`sort` package
```go
import (
	"fmt"
	"math/rand"
	"sort"
)
func main() {
	scores := make([]int, 100)
	for i := 0; i < 100; i++ {
		scores[i] = int(rand.Int31n(1000))
	}
	sort.Ints(scores) // sort integers method

	worst := make([]int, 5)
	copy(worst, scores[:5]) // copies first five elements from scores
}
```
- Sort methods are specific to the builtin type; e.g. `sort.Strings` or `sort.Ints`
- sorting is in-place, so it changes the given slice and doesn’t return a new one.

# Gotchas
1. Slices of a slice will refer to the same underlying array.
	1. https://goplay.tools/snippet/4vICYdQr-kl
2. Appending to a slice will not always cause data to be copied to a new memory location. Copying only happens when a larger capacity is needed to accommodate new data. 
Solution: pass a copy of the slice or reslice of exact size
https://goplay.tools/snippet/AtqDWvfM3zx
```go
	// Be careful of ghost updates
	// Append function takes a copy of the slice ONLY if a greater capacity is needed
	a := []int{1, 2, 3, 4}
	b := a[:2]
	c := a[2:]
	b = append(b, 100) // cap(b) = 4, therefore append will not reallocate and overwrites instead.

	// a = [1 2 100 4]
	// b = [1 2 100]
	// c = [100 4]
	fmt.Println(a, b, c)

	// If you are passing around slices that might be appended to, it's safest to limit the capacity on slice, so that you guarantee appending will reallocate.
	keepThisSlice := append([]int{}, 0, 1, 2, 3, 4) // cap = 6
	fmt.Println("capacity:", cap(keepThisSlice))

	// take a slice of the exact size so appending will cause copying.
	funcThatMayGrowSlice(keepThisSlice[:5:5])
```




---
Tags: [Go Lang](./Go%20Lang.md) -[Programming](Programming.md) 

Reference: https://blogtitle.github.io/go-slices-gotchas/
Notes from [slices intro](https://go.dev/blog/slices-intro): https://goplay.tools/snippet/hoHMjI-bUyW

Related: 
