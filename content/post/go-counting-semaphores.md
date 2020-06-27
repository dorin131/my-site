---
title: "Go: counting semaphores"
draft: false
date: 2019-12-20T21:45:00Z
slug: "go-counting-semaphores"
tags: ["go", "semaphore", "channels", "buffered"]
---

### Goroutines

One of the main features of the Go programming languages is the goroutine. Executing a function as a goroutine lets us run things in parallel which can greatly improve the performance of our application. But you can take parallelism only so far before it actually starts making things slower.


### Unlimited parallelism

Your code might be calling a function called `doInParallel()` as a goroutine in this fashion:

```go
func main() {
  wg := sync.WaitGroup{}
  defer wg.Wait()
  for n := range []int{0,1,2,3,4,5,6,7,8,9} {
    wg.Add(1)
    go doInParallel(&wg, n)
  }
}

func doInParallel(wg* sync.WaitGroup, n int) {
  fmt.Println(n)
  time.Sleep(1000 * time.Millisecond)
  defer wg.Done()
}

```
Repl: [https://repl.it/@dorin131/goroutine](https://repl.it/@dorin131/goroutine)

In this example we are just taking an element of an array, printing it and waiting for one second. All the numbers will be printed immediately and at the same time.


### Limited parallelism with counting semaphores

But how do we limit how many instances of the `doInParallel()` we are executing at the same time?
The example below will do that using a concurrency primitive called a **counting semaphore** which is implemented using a buffered channel.


```go
var tokens = make(chan struct{}, 3) // allowing up to three elements in the buffer

func main() {
  wg := sync.WaitGroup{}
  defer wg.Wait()
  for n := range []int{0,1,2,3,4,5,6,7,8,9} {
    wg.Add(1)
    go doInParallel(&wg, n)
  }
}

func doInParallel(wg* sync.WaitGroup, n int) {
  tokens <- struct{}{} // adding an element on the channel
  fmt.Println(n)
  time.Sleep(1000 * time.Millisecond)
  <- tokens // discarding one element from the channel
  defer wg.Done()
}

```
Repl: [https://repl.it/@dorin131/counting-semaphore](https://repl.it/@dorin131/counting-semaphore)

By adding an element just before our I/O operation and discarding one right after, we are essentially blocking the program once the channel buffer is full. Then after one second the buffer will be emptied and it continues.

If you run the second snippet, you will see that the numbers are printed three at a time with a pause of one second in-between.


### Conclusion

Choosing how many threads you want to spawn at the same time will depend on the task you're performing and the machine it is running on. Once you have calculated an approximate number, it is worth trying out the code with higher or lower values to see what works best.

Also, just to make sure this was not misinterpreted. The limitation comes from the operation that you are performing in parallel and not the goroutines in themselves, as you can run many thousands without any issue.

