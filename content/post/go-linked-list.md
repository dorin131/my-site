---
title: "Go data structures: Linked List"
draft: false
date: 2019-12-22T22:50:00Z
slug: "go-linked-list"
tags: ["go", "data structure", "linked list"]
---

### Definition
> A linked list is a linear collection of data elements, whose order is not given by their physical placement in memory. Instead, each element points to the next. It is a data structure consisting of a collection of nodes which together represent a sequence. *(https://en.wikipedia.org/wiki/Linked_list)*

### A little intro
This is a fist post from a series on Data Structure implementations in Go. I'm starting with what I consider one of the simplest data structures (I might be proven wrong later) and will continue with some of the other popular ones.

The full code will be available on GitHub and I will break it down here in as small chunks as I see necessary and explain what was done.

{% github github.com/dorin131/go-data-structures no-readme %}

This is not meant to be production code, so there is no error handling, it is meant for educational purposes only.

With all this said, let's get into it! :)


### Implementation

To start with, we are going to create a struct which will contain a field `Head` with a reference to the first node of the list and another field `Tail` pointing to the last node. Also, one more struct will be created which represents the node itself. The node will be holding a reference to the following node and its own data. Notice how the type for `Data` is `interface{}`, which means that it can be of any type.

```go
// LinkedList : Data structure
type LinkedList struct {
	Head *Node
	Tail *Node
}

// Node : A Linked List node
type Node struct {
	Next *Node
	Data interface{}
}
```

Following this, we want to create a "constructor" function which is just going to return a pointer to a new instance of a Linked List, with an empty node as `Head` and the exactly same node as `Tail`. You will see later that we are going to check whether our node is empty or not when performing some operations.

```go
// New : Create a new Linked List
func New() *LinkedList {
	emptyNode := &Node{
		Next: nil,
		Data: nil,
	}
	return &LinkedList{
		Head: emptyNode,
		Tail: emptyNode,
	}
}
```

The first method we are going to add is `Append`. It has a pointer receiver which will let us modify the instance. In our case, we want to add a value to the last nodes `Next` field, which will place a new node at the end of our Linked List.

At the top we create a new node that doesn't have a `Next` value because it will be the last one and set the data argument to the `Data` field.

Notice how we check whether our first node is empty, and in that case our new node will be set as the `Head`, other wise it will be set as the next node of the `Tail`.

```go
// Append : Appending a new node to the end of the Linked List
func (ll *LinkedList) Append(d interface{}) *LinkedList {
	nextNode := &Node{
		Next: nil,
		Data: d,
	}
	if ll.Head.Data == nil {
		ll.Head = nextNode
	} else {
		ll.Tail.Next = nextNode
	}
	ll.Tail = nextNode
	return ll
}
```

Another method I wanted to add was one which would let us delete a node which holds a specific value (only the first instance in this case). I called this method `DeleteWithValue`.

First we check whether the first element matches the value and then in a loop we check every `Next`'s node value. We look one node ahead so that we can modify previous node's `Next` reference to point to the node after next, basically skipping it.

```go
// DeleteWithValue : Deletes node which has a specific value
func (ll *LinkedList) DeleteWithValue(v interface{}) *LinkedList {
	var node = ll.Head
	if node.Data == v {
		ll.Head = ll.Head.Next
		return ll
	}
	for {
		if v == node.Next.Data {
			if node.Next.Next != nil {
				node.Next = node.Next.Next
				return ll
			}
			node.Next = nil
			return ll
		}
		node = node.Next
	}
	return ll
}
```

Finally, I added a `PrintAll` method which prints all the nodes, starting with the first one, to the standard output.

```go
// PrintAll : Prints all elements of the Linked List
func (ll *LinkedList) PrintAll() {
	var node = ll.Head
	for {
		fmt.Println(node.Data)
		if node.Next == nil {
			return
		}
		node = node.Next
	}
}
```

If you want to test the this package, then you can clone the GitHub repo linked at the beginning of this post and run `go test ./linkedlist` from the root directory. There are a few "Example" tests which illustrate the usage and test it at the same time.


### Conclusion

The Linked List was a fun data structure to implement but it's not the most practical or efficient out there. The main disadvantages would be slow indexing and a larger memory footprint. By having a pointer to the last element in the list (like we had) and a pointer on every node to the previous element (not implemented) we could speed up some operations but it would still lag behind.
