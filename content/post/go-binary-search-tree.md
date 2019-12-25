---
title: "Data Structures in Go: Tree"
draft: false
date: 2019-12-25T19:48:00Z
slug: "go-binary-search-tree"
tags: ["go", "data structure", "tree"]
---

### Introduction

A **Tree** is a non-linear data structure which consists of one or more nodes where a node can have zero or more references to other nodes which will form a sub-tree.

There are multiple types of trees: ordered, un-ordered, balanced, non-balanced, with two children, with more than two children, etc.

For the purpose of this article I have chosen to implement a non-balanced **Binary Search Tree**, which is an ordered tree with two child nodes. The one on the left will always contain a smaller value and the one on the right a greater value.

### Implementation

To start with, we are going to create a struct which is going to represent a single node of our tree. There will always be a node at the top, to which everything else is going to be added.

```go
// Node : represents a single node of a Tree data structure
type Node struct {
	Left  *Node
	Right *Node
	Data  int
}
```

Next we add a constructor function `New` which will take the value of the first node and return an instance of a tree node.

```go
// New : constructor function for a Tree node
func New(data int) *Node {
	return &Node{
		Left:  nil,
		Right: nil,
		Data:  data,
	}
}
```

Now we are going to add a few methods to our tree node struct. These are going to be: `Insert`, `Contains` and `Traverse`.

The `Insert` method is going to add a node with the value passed to it, to the tree. We are going to recursively look into all the nodes until we find a suitable location with a *nil* reference where we can append the new node.

```go
// Insert : adds a node to the tree
func (t *Node) Insert(data int) *Node {
	if data < t.Data {
		if t.Left != nil {
			t.Left.Insert(data)
		} else {
			t.Left = New(data)
		}
	} else {
		if t.Right != nil {
			t.Right.Insert(data)
		} else {
			t.Right = New(data)
		}
	}
	return t
}
```

Then we add the `Contains` method which is going to start traversing the tree and look for a node with a specific value. It will return a boolean with the success status.

```go
// Contains : checks if the tree contains a node with a given value
func (t *Node) Contains(data int) bool {
	if t.Data == data {
		return true
	}
	if data < t.Data {
		if t.Left != nil {
			t.Left.Contains(data)
		} else {
			return false
		}
	} else {
		if t.Right != nil {
			t.Right.Contains(data)
		} else {
			return false
		}
	}
	return false
}
```

And lastly we add the `Traverse` method which is going to traverse the entirety of the tree and print all its values to the standard output.

```go
// Traverse : prints all elements of the tree
func (t *Node) Traverse() {
	if t.Left != nil {
		t.Left.Traverse()
	}
	fmt.Println(t.Data)
	if t.Right != nil {
		t.Right.Traverse()
	}
}
```

### Conclusion

We ended up with a basic Binary Search Tree which will let us add any element to it with a complexity of O(n) and search, in the worst case also with O(n). This is not ideal and it is possible to improve the tree by making sure it is balanced. A balanced tree would let us search at O(log(n)).

Stay tuned for new posts on how to balance our tree!

### Source code with tests
https://github.com/dorin131/go-data-structures