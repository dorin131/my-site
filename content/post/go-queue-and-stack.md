---
title: "Data Structures in Go: Queue and Stack"
draft: false
date: 2019-12-24T18:45:00Z
slug: "go-queue-and-stack"
tags: ["go", "data structure", "queue", "stack"]
---

### Introduction
Two of the most basic data structures are the **Queue** and the **Stack**. These can be implemented in many different ways and their underlying data structures could also be of various types. Usually they are based on either linked lists or arrays.

In our case I have chosen to base these data structures on *slices*, as this is a native type in Go and is very easy to manipulate (grow, slice, etc.), also very efficient.

Slices are based on *arrays* but are more flexible, have a dynamic length and use pointers to retrieve data.

### The Queue

Below is the entire code for the basic queue implementation. You can see that we have created a **Queue** struct which contains a `data` field of type *slice*. This field is going to hold all the queue values.

We've also added some methods: `IsEmpty`, `Peek`, `Queue` and `Dequeue`.


```go
/*
Package queue provides a slice-based Queue data structure
*/
package queue

import "fmt"

// Queue : data structure
type Queue struct {
	data []int
}

// New : returns a new instance of a Queue
func New() *Queue {
	return &Queue{
		data: []int{},
	}
}

// IsEmpty : checks whether the queue is empty
func (q *Queue) IsEmpty() bool {
	return len(q.data) == 0
}

// Peek : returns the next element in the queue
func (q *Queue) Peek() (int, error) {
	if len(q.data) == 0 {
		return 0, fmt.Errorf("Queue is empty")
	}
	return q.data[0], nil
}

// Queue : adds an element onto the queue and returns an pointer to the current queue
func (q *Queue) Add(n int) *Queue {
	q.data = append(q.data, n)
	return q
}

// Dequeue : removes the next element from the queue and returns its value
func (q *Queue) Remove() (int, error) {
	if len(q.data) == 0 {
		return 0, fmt.Errorf("Queue is empty")
	}
	element := q.data[0]
	q.data = q.data[1:]
	return element, nil
}
```

### The Stack

The stack has an identical underlying structure as the queue - a *slice*. Although the methods are slightly different: `IsEmpty`, `Peek`, `Push`, `Pop`.

Notice how we are using LIFO (last in , first out) principle here.
```go
/*
Package stack provides a slice-based Stack data structure
*/
package stack

import "fmt"

// Stack : data structure
type Stack struct {
	data []int
}

// New : returns a new instance of a stack
func New() *Stack {
	return &Stack{
		data: []int{},
	}
}

// IsEmpty : will return a boolean indicating whether there are any elements on the stack
func (s *Stack) IsEmpty() bool {
	return len(s.data) == 0
}

// Peek : will return the element on the top of the stack
func (s *Stack) Peek() (int, error) {
	if s.IsEmpty() {
		return 0, fmt.Errorf("Stack is empty")
	}
	return s.data[len(s.data)-1], nil
}

// Push : Adds an element on the stack
func (s *Stack) Push(n int) *Stack {
	s.data = append(s.data, n)
	return s
}

// Pop : removes an element from the stack and returns its value
func (s *Stack) Pop() (int, error) {
	if len(s.data) == 0 {
		return 0, fmt.Errorf("Stack is empty")
	}
	element := s.data[len(s.data)-1]
	s.data = s.data[:len(s.data)-1]
	return element, nil
}
```

### Conclusion
As you have probably noticed, the Stack and the Queue are quite similar and the main difference is the order in which we retrieve the data: FIFO (queue) and LIFO (stack).

### Source code with tests
https://github.com/dorin131/go-data-structures