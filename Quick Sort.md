---
creation date: 2022-04-04 15:49
aliases: 
tags: 🖥️
---

# [Quick Sort](Quick%20Sort.md)
*[Quick Sort](Quick%20Sort.md)* is a  recursive divide and conquer sorting algorithm
- usually used under the hood as built-in sorting method because of its speed. 
- $O(N * logN)$. 
- utilizes a combination of recursion and [partitioning](Quick%20Sort.md#partitioning)

###### Algorithm
1. Partition the array. Pivot is now sorted.
2. Partition the left and right subarrays, recursively repeating steps 1 & 2. 
3. When subarray has 0 or 1 elements, we've reached our base case. The array is sorted.

Quick sort performance for worst case is similar to insertion or selection sort, but ==much faster for average case scenarios than selection or insertion==.

Each time the array is partitioned, it is broken down into two subarrays. In the average/best cases, the pivot ends up near the middle and the subarrays are broken into near-equal halves:  *how many times can we break an array into halves until we've broken it down completely to size of 1?*

💡This is the definition of $log N$! We have $log N$ halvings, and for each halving, we partition all the subarrays, whose elements add up to $N$, the original.

- Each partition involves comparisons (left and right pointers to pivot) and swaps.
- Each partition will have at least $N$ comparisons (each element will be compared) and at most $N/2$ swaps (if we swapped every single element, two at a time).
- The partition process repeats on successively smaller subarrays, and the best/average cases are when the pivot is sorted to the middle of the array ranges.

###### Partitioning
Partitioning is a process that takes a random value from the array (the pivot) and  ensures that every number that is *less* than the pivot ends up to the left of it and every number *greater* than the pivot is to the right. 
- It also guarantees that the pivot element is correctly sorted.

💡This doesn't mean it's perfectly sorted in one go, but it's sort of like binary search in that the left and right halves are guaranteed over/under the mid-point (pivot). 

###### Pseudocode
1. Select the right most value in array range as the pivot (technically can pick any number). Every element is compared to the pivot, and the pivot is excluded from the pointers.
2. A left pointer starts at the left-most value of the array range and compares each element to the pivot until it reaches a element that is *greater* than pivot.
	1. If the pointer value is *less* than the pivot, the left pointer moves right one element. 
3. Once the left pointer stops, the right pointer activates and keeps moving left until it encounters an element that is *less* than pivot.
4. If the left pointer and right pointer haven't met yet, *swap the elements* that each pointer is pointing to. 
5. Otherwise, if the left pointer is pointing at the same element OR *has gone beyond* the right pointer, skip to step 7.
6. Reactivate left pointer to repeat steps 2 & 3
7. When left and right pointers point at the same element or crossed, swap left pointer element with the pivot. 

![Pasted image 20220404152046.png](./images/Pasted%20image%2020220404152046.png)

###### Code
```js
class SortableArray {
  constructor(array) {
    this.array = array
  }

  quicksort(left_index, right_index) {
    // base case: subarray has - or 1 elements
    if (right_index - left_index <= 0) return

    // parition range and use pivot location to split into subarrays
    const pivot_index = this.partition(left_index, right_index)

    // Recursively call quicksort on elements left and right or pivot (subarrays)
    this.quicksort(left_index, pivot_index - 1)
    this.quicksort(pivot_index + 1, right_index)

  }

  partition(left_pointer, right_pointer) {
    // always choose right-most element as pivot
    const pivot_index = right_pointer
    let pivot = this.array[pivot_index]

    // start right pointer immediately to left of pivot
    right_pointer--

    while (true) {
      // move left pointer to the right as long as element < pivot
      while (this.array[left_pointer] < pivot) {
        left_pointer++
      }
      // move right pointer left as long as eleemnt > pivot
      while (this.array[right_pointer] > pivot) {
        right_pointer--
      }
      // if left pointer has reached or gone beyond right pointer, break out
      if (left_pointer>= right_pointer) break

      // swap pointer values if left pointer is still left of right pointer
      [this.array[left_pointer], this.array[right_pointer]] = [this.array[right_pointer], this.array[left_pointer]]

      // move left pointer to prepare for next iteration of pointer movements
      left_pointer++
    }

    // final step of partition: swap value of left pointer with pivot
    [this.array[left_pointer], this.array[pivot_index]] = [this.array[pivot_index], this.array[left_pointer]]

    // return left pointer to be used in quicksort method
    return left_pointer
  }
}

let sortable = new SortableArray([0, 5, 2, 1, 6, 3])
sortable.quicksort(0, sortable.array.length - 1)

console.log(sortable.array);
```

