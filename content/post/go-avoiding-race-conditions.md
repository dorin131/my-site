---
title: "Avoiding race conditions in Go"
draft: false
date: 2019-12-21T19:30:00Z
slug: "go-avoiding-race-conditions"
tags: ["go", "concurrency", "race conditions"]
---

### Intro
Being able to use goroutines correctly is one of the most important skill for a go developer. They give you great power which is easy to invoke but not everyone can grasp the consequences of mishandling it.

### Concurrency-safety

A function is said to be *concurrency-safe* if it can run correctly when called concurrently, meaning from multiple goroutines.


### Race conditions
A race condition could occur when two or more goroutines access a resource (variable) concurrently and at least one of them executes a write operation to it.

Even though the execution order within one goroutine is fixed, when several of them are called, we cannot know anymore what operations are executed in what order amongst them, which can produce nasty bugs that are hard to fix and even harder to reproduce.

One way we can get help from the compiler, is to add the `-race` flag to your `build`, `run` or `test` commands, which will make it attempt to detect any potential race conditions in your code and report them.

Below is an example of a program that keeps track of the number of gophers in a zoo:

```go
var totalGophers = 0

func AddGophers(n int) {
  totalGophers = totalGophers + 1;
  fmt.Printf("Total: %d\n", n)
}

```

The `addGophers` function above is **not concurrency-safe** because we are writing to a shared variable `totalGophers` and have no measures in place to prevent race conditions.

Imagine that two goroutines run in parallel and when they request the `totalGophers` value they get the same value, this will result in one of them overwriting the gopher number from the other and the total will be completely wrong.


### Solutions
There are two ways to ensure that our functions are concurrency-safe and will not cause a race condition:
- Confining the shared resources in one goroutine and using channels to get requests and send responses.
- Using mutexes to prevent more than one goroutine from reading/writing a shared resource.

#### Confinement
Taking the example above, we can confine the `totalGophers` variable in a separate goroutine and use channels to do our reads and writes. Therefore only *one* goroutine is going to have access to it.

```go
var addition = make(chan int)
var total = make(chan int)

func main() {
  go zookeeper()
}

func AddGophers(n int) {
  addition <- n
}

func GetTotal() {
  fmt.Printf("Total: %d\n", <- total)
}

func zookeeper() {
  var totalGophers = 0
  for {
    select {
      case gophers := <- addition:
        totalGophers += gophers
      case total <- totalGophers:
    }
  }
}

```

What we see above is that we confined the `totalGophers` variable in a goroutine named `zookeeper` which watches two channels: `addition` and `total`.

Whenever a number of gophers is sent to the `addition` channel, we add that to the `totalGophers` and the `total` channel will always contain the total value.

This one possible solution to avoid race conditions. 

#### Mutexes
Another option is to use mutexes, which are provided by the `sync` package.

A mutex will lock an operation in the code so that it can be accessed by only one goroutine. If you have read my previous post on [Counting Semaphores](https://dev.to/dorin/go-counting-semaphores-j4a), then you can regard the mutex as a binary semaphore or a semaphore with a channel that has a buffer of 1.

Here is how our gopher zoo program would look using this method:

```go
var totalGophers = 0
var mu sync.Mutex

func addGophers(n int) {
  mu.Lock()
  defer mu.Unlock()
  totalGophers = totalGophers + 1;
  fmt.Printf("Total: %d\n", n)
}
```

### Food for thought
Now that you have an idea of how to go about making your functions concurrency-safe, you should make sure that you always take the required measures.

The examples above are probably simpler than the application that you are going to work on, so it can get much more complex. Do not be fooled by the apparent simplicity of using mutexes, because in a real life situation it can get more difficult to manage them. One of the pitfalls could be that you are basically making your goroutine execution synchronous, which defeats the purpose of using them in the first place.

The last thing I want to say is that this should be just a starting point for your research into avoiding race conditions.