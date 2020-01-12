---
title: "Algorithms in Go: Heap Sort"
draft: false
date: 2020-01-12T22:30:00Z
slug: "go-heap-sort"
tags: ["go", "algorithms", "sort", "heap sort"]
---

### Introduction
Heap Sort is a sorting algorithms which uses the [Heap data structure](https://dev.to/dorin/data-structures-in-go-heap-191j) to arrange a set of elements in an ascending or descending order.

In a previous post I have shown how to build a Max Heap. I suggest that you check it out first before continuing with the sorting algorithm.

[https://fodor.org/blog/go-heap/](https://fodor.org/blog/go-heap/)

As you know now, there are two main types of heaps: Max Heap and Min Heap. Once we have implemented those, it is a trivial matter to do the sorting.

In a Max Heap, we are going to always have the largest element at the top, followed by the smaller ones. And in a Min Heap, the opposite is going to be true. Hence we are going to use the Max Heap for sorting in a descending order and the Min Heap for sorting in an ascending order.

### Time complexity
The time complexity of a heap sort is O(*n* lg *n*), since the initial processing of the input into a heap takes O(*n*) and for every element extraction we need to make adjustments that take O(lg *n* ).

### How does it work?
If we want to sort a set of elements in ascending order then we:
1. Create a Min Heap from the elements. This is going to place the smallest element at the top.
2. Extract the minimum (top) element until we exhaust the heap and place them in a new array/slice, sequentially.
3. The resulting array/slice will automatically be sorted in an ascending order.

The sort in a descending order, we are going to follow the same steps as above, but instead will create a Max Heap.

### Implementation
In this implementation, I have used the [Heap data structures](https://github.com/dorin131/go-data-structures) I have created earlier and created two methods: `SortAscending` and `SortDescending`.

```go
package heapsort

import (
	"github.com/dorin131/go-data-structures/maxheap"
	"github.com/dorin131/go-data-structures/minheap"
)

// SortAscending : sort ascending a slice of integers using Heap Sort
func SortAscending(input []int) []int {
	result := []int{}

	mh := minheap.New(input)

	for range input {
		result = append(result, mh.ExtractMin())
	}

	return result
}

// SortDescending : sort descending a slice of integers using Heap Sort
func SortDescending(input []int) []int {
	result := []int{}

	mh := maxheap.New(input)

	for range input {
		result = append(result, mh.ExtractMax())
	}

	return result
}

```

To test that our sorting works correctly, I have used Go's built-in `sort`  library.

```go
func TestSortAscending(t *testing.T) {
	tests := []struct {
		Given []int
	}{
		{[]int{4, 9, 10, 0, -4, 7}},
		{[]int{1000, 100, 10, 0, -1000}},
		{[]int{1, 1, 1, 1}},
		{[]int{}},
		{[]int{777}},
	}
	for n, test := range tests {
		correctlySorted := make([]int, len(test.Given))
		copy(correctlySorted, test.Given)
		result := SortAscending(test.Given)

		if len(result) != len(test.Given) {
			t.Errorf("[%d] Lengths don't match", n)
			return
		}

		sort.Slice(correctlySorted, func(i, j int) bool {
			return correctlySorted[i] < correctlySorted[j]
		})

		for k := range correctlySorted {
			if correctlySorted[k] != result[k] {
				t.Errorf("[%v] Expected: %v, Got: %v\n", n, correctlySorted, result)
				return
			}
		}
		fmt.Printf("[%v] Correctly sorted: %v\n", n, result)
	}
}
```

### Source code
https://github.com/dorin131/go-algorithms

### To follow
Stay tuned for the next post which is going to be on Priority Queues, which are also based on Heaps!