---
creation date: 2022-03-25 13:38
aliases: 
tags: 🖥️
---
###### Description
**Bubble Sort** - one of the slowest
- 👎Worst case:  $O(N^2)$
- Time complexity = $comparisons + swaps$ 
- Keeping making passes through an array, swapping pairs until no more swaps occur
- We are both iterating through an array to do *comparisons* of pairs, and also *swapping* values when the pair is out of order. 
- highest value gets "bubbled" up to the correct position first

######  Implementation
```go
func bubblesort(arr []int) []int {
	swaps := 0
	comparisons := 0
	sorted := false
	unsorted_until_index := len(arr) - 1

	for !sorted {
		sorted = true

		for i := 0; i < unsorted_until_index; i++ {
			comparisons++
			if arr[i] > arr[i+1] {
				arr[i], arr[i+1] = arr[i+1], arr[i]
				sorted = false
				swaps++
			}
		}
		unsorted_until_index--
	}
	fmt.Printf("For %v length array, Total steps: %v\n", len(arr), swaps+comparisons)
	return arr
}
```

