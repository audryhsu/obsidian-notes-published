---
creation date: 2022-03-25 15:03
aliases: 
tags: 🖥️
---

###### Description [Selection Sort](Selection%20Sort.md)
Time complexity: $O(N^2)$ in Big O notation
- Total steps = $comparisons + swaps$
- Actually, selection sort is *twice as fast as [Bubble Sort](./Bubble%20Sort.md)* since there is at most, one swap for each comparison as opposed to $N-1$...etc swaps in bubble sort, or $O(N^2/2)$
- Big O doesn't care about constants (non-exponents), though, so in Big O, selection sort and bubble sort are the "same".
- Lowest value gets sorted to beginning first (opposite of bubble)

Algorithm:
1. For each index position in the array, compare the elements in pairs to find the lowest value
2. Lowest is initialized to element at `arr[i]`
3. Save the index location of lowest
4. At the end of the comparison, swap the element at index `i` with the element at  `indextLowest`
5. Keep looping until second to last element in the array
###### Implementation
```go
func selectionsort(arr []int) []int {
	compares := 0
	swaps := 0

	for i := 0; i < len(arr)-1; i++ {
		indexLowest := i
		lowest := arr[i]

		for j := i + 1; j < len(arr); j++ {
			compares++
			if arr[j] < lowest {
				lowest = arr[j]
				indexLowest = j
				swaps++
			}
		}
		// swap el at current index with lowest index
		arr[i], arr[indexLowest] = arr[indexLowest], arr[i]
	}
	fmt.Printf("%v compares + %v swaps = %v total steps\n", compares, swaps, compares+swaps)
	return arr
}
```

