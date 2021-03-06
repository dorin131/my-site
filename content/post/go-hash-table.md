---
title: "Data Structures in Go: Hash Table"
draft: false
date: 2019-12-28T16:40:00Z
slug: "go-hash-table"
tags: ["go", "data structure", "hash table"]
---

### Introduction

The time has come to implement one of the most commonly used data structures in software development - the Hash Table!

A hash table is a data structure that implements an associative array, allowing us to store pairs of keys and values that can be accessed at an expected O(1) time (O(n) - worst case for a bad implementation).

Personally I love this data structure. It works a bit like magic! How could you access a random key's value at constant time?

Here is how it works:
1. Create an array of a length equal to the expected number of key/value pairs in your hash table. The bigger the array, the lower the chance of collisions (explained further below).
2. Create a hashing function which will take the value of the key you want add and transform it into a number. The better this function is, the lower the chance of collisions.
3. Take the number generated by the hashing function and calculate the modulo with the array length. (e.g. if the hash is 1234 and the array length is 100, then calculate 1234 % 100). This is going to be the index in the array where you are going to store the value.

That is pretty much how it works in a nutshell. One of the most important things in a hash map is probably the hashing function. It can make the difference between a great and a very poor hash map.

In the implementation that follows, for a hash table that allows only string keys, I chose to use the following hash function:
`char[0] * 31^n-1 + char[1] * 31^n-2 + ... + char[n-1]`

Also, I have mentioned above **collisions** a couple of times. A collision happens when the array indices for two different keys happens to be the same. This could be because of a bad hashing function or a too small of an underlying array. Both of these can be fixed though. You can use a better hashing function and you can also dynamically grow the array when needed.

But collisions *will* happen, so then what do we do about it? Instead of storing our value as is in the array, we are going to store it in a linked list (or array) together with the un-hashed key. So, whenever we try to retrieve or modify a key value, we walk through that linked list and make sure that we are manipulating the correct key (if there are multiple at that index).

Luckily we have implemented a linked list already! And we're going to use it here :)

https://fodor.org/blog/go-linked-list/


### Implementation
To start with, we are going to choose an array size for the underlying data store. In the current implementation this is going to stay fixed, but a more advanced version of this would be able to dynamically create a larger array once the number of keys reaches the length of the array.

```go
const arrayLength = 100
```

Then as always we create a struct for out data structure. The only field we have is for the array hose every element will be a pointer to a linked list (github.com/dorin131/go-data-structures/linkedlist).

```go
type HashTable struct {
	data [arrayLength]*linkedlist.LinkedList
}
```

Next we create another struct type for the contents of the linked list nodes. In this implementation, the key has to be a string and the value can be any type.

```go
type listData struct {
	key   string
	value interface{}
}
```

Also we add a constructor function.

```go
func New() *HashTable {
	return &HashTable{
		[arrayLength]*linkedlist.LinkedList{},
	}
}
```

And like we discussed earlier we are going to need a hashing function and a function to get the index for a hashed key.

```go
func hash(s string) int {
	h := 0
	for pos, char := range s {
		h += int(char) * int(math.Pow(31, float64(len(s)-pos+1)))
	}
	return h
}

func index(hash int) int {
	return hash % arrayLength
}
```

Now it's time to add our `Set` and `Get` methods. These are going to let us add a key to the map and retrieve the value of a key respectively.

In the `Set` method, we first calculate the index for our key, by first hashing it and then getting the modulo. Then if there is nothing at that position already then we initialise a new linked list, otherwise we iterate through the list and check whether we need to update an existing value or add a new one.

```go
func (h *HashTable) Set(k string, v interface{}) *HashTable {
	index := index(hash(k))

	if h.data[index] == nil {
		h.data[index] = linkedlist.New()
		h.data[index].Append(listData{k, v})
	} else {
		node := h.data[index].Head
		for {
			if node != nil {
				d := node.Data.(listData)
				if d.key == k {
					d.value = v
					break
				}
			} else {
				h.data[index].Append(listData{k, v})
				break
			}
			node = node.Next
		}
	}
	return h
}
```

And for the `Get` method we start the same, by calculating the index and then we look through the linked list (if it exists) and look for our value.

```go
func (h *HashTable) Get(k string) (result interface{}, ok bool) {
	index := index(hash(k))
	linkedList := h.data[index]

	if linkedList == nil {
		return "", false
	}
	node := linkedList.Head
	for {
		if node != nil {
			d := node.Data.(listData)
			if d.key == k {
				return d.value, true
			}
		} else {
			return "", false
		}
		node = node.Next
	}
}
```

### Conclusion
The next steps with the current implementation would be to make our underlying array expandable. But I'll leave that to you. Let me know how you went about doing that!

### Source code with tests
https://github.com/dorin131/go-data-structures
