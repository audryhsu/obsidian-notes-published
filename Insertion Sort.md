---
creation date: 2022-03-25 16:14
aliases: 
tags: 🖥️
---

###### Description [Insertion Sort](Insertion%20Sort.md)
- Worst case: $O(N^2)$
- Major actions that contribute to Big O = $comparisons + shifts$
	- goal is to shift elements and insert the current element as far left as possible
- in reality, insertion sort is $O(N^2 + 2N)$, but we drop both constants and the lower order of N for Big O
###### Implementation
- Iterate through the array starting at index 1 and save to a temp variable
- Compare temp variable to elements that come *before* the element
	- If any prior element is greater than current element, remove and shift the greater element to the right
	- Keep comparing until current element as far left as possible. 
	- If the prior element is less than current element, no shifts occur.
```go
func insertionsort(arr []int) []int {
	compares := 0
	shifts := 0
	for i := 1; i < len(arr); i++ {
		temp := arr[i]

		for j := i - 1; j >= 0; j-- {
			if temp > arr[j] {
				break
			}
			arr[j+1] = arr[j]
			arr[j] = temp
			shifts++
		}
		compares++
	}

	fmt.Printf("%v compares + %v shifts = %v", compares, shifts, compares+shifts)
	return arr
}
```

![Pasted image 20220325171856.png](./images/Pasted%20image%2020220325171856.png)
![Pasted image 20220325171900.png](./images/Pasted%20image%2020220325171900.png)