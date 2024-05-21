---
creation date: 2022-03-25 12:25
aliases: 
tags: 🖥️
---
## Binary Search
**Array must be sorted** to use binary search
- Tradeoff: less efficient *insertion* for $O(log N)$ search
- ✖ Insertion is $N + 1$
	- requires a search operation first (to determine where the new value should), then an insertion. There's a trade off between where it gets inserted (beginning or end) and number of shifts vs elements to search. 
- ☑️Searching time complexity is  $O(log N)$
	- Each time we double the number of elements, our time complexity steps only increases by *one* since we can eliminate *half* of the set each time. Talk about value!!
	- 🔑 In constant time, we need to know whether to go left or right
- $O(log N)$ - repeatedly chop in half in *constant* time. Need to array sorted to know whether to go left or right

##### Template
- If you already have a linear O(N) solution and the interviewer asks whether you can do better, it's most likely going to involve binary search! 
- ==Even if the array is not ordered, it's still possible to do Binary Search if you can decide which half to keep with a O(1) operation==
	- Example: using the length of array to determine left or right  

Two points of interest for the below code:
- we're using some seemingly unnecessary parentheses to calculate the midpoint. This is to guard against overflow issues (ex: signed integers in C). If you add left + right, there's a small chance it might overflow, so we subtract first; they're mathematically equivalent and not something you have to worry about usually.
- in this algorithm, the two pointers are right next to each other after the loop. Just have to remember to see which one is the target.
```go
left, right := 0, len(array)-1
for left <= right {
  mid := left + ((right - left) / 2) // prevents overflow
  if array[mid] == target {
    // optional early return
  } else if ***comparison*** {
    left = mid + 1
  } else {
    right = mid - 1
  }
}

// Most often once you get here, the mid wasn't found 
// so you need to return a certain value depending on the problem
```

###### [Binary Search](Binary%20Search.md) (Go)
```go
import (
	"fmt"
	"sort"
)

func bsearch(arr []int, target int) int {
	// must be sorted
	sort.Ints(arr)
	lower := 0
	upper := len(arr) - 1
	for lower <= upper {
		middle := (lower + upper) / 2

		if arr[middle] == target {
			fmt.Printf("Found %v at index %v", target, middle)
			return middle
		} else if target < arr[middle] {
			upper = middle - 1
		} else {
			lower = middle + 1
		}
	}
	fmt.Printf("%v not present in array", target)
	return -1
}

func main() {
	bsearch([]int{300, 55, 73, 2, -1000}, -1000)
}
```
Algorithm:
1. Start with a sorted array
2. Initialize the lower and upper bounds
3. While there is a range of elements, check the value at the midpoint
4. If the target is less than the midpoint, move the upperbound
5. Else if the target is greater than midpoint, move the lowerbound
6. If target not found when lowerbound == upperbound (i.e. only one element in range), then value doesn't exist
