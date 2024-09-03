---
layout: post
title: How to reduce garbage collection pressure in Go?
description: Object pooling with sync.Pool in Go
date: 2024-09-03 21:38 +0800
categories: [go, performance]
tags: [go, performance, garbage collection, sync.pool]
---

## Background

At work today, one of my colleagues used `sync.Pool` in their code. I was curious about how it works and why it's useful. In fact, I have never seen it used before. So, I decided to do some research and write about it.

## Introduction

### What is garbage collection?

Garbage collection is a form of memory management. It is a technique used by programming languages to automatically free memory that is no longer needed. In Go, the garbage collector is responsible for managing memory allocation and deallocation.

Some languages, like C and C++, require manual memory management, where the programmer is responsible for allocating and freeing memory. This can lead to memory leaks and other issues if not done correctly.

### What kind of memory is managed by the garbage collector?

There are many types of memory that a program uses. Some examples include:

1. Stack memory: Used for function calls and local variables.
2. Heap memory: Used for dynamically allocated memory, such as objects and arrays.
3. Text memory: Used for the program's instructions.
4. Data memory: Used for global and static variables.

The garbage collector in programming languages like Go is responsible for managing heap memory.

### What is garbage collection pressure?

Garbage collection pressure is the amount of work that the garbage collector has to do to free up memory. When the garbage collector runs, it has to stop the program (including all threads), scan the heap, and free up memory that is no longer needed. This can be a time-consuming process, especially if there is a lot of memory to free up.

For example, if a progam creates a lot of short-lived objects that are no longer needed, the garbage collector will have to run more frequently to free up memory.

### How can we reduce garbage collection pressure?

One way to reduce garbage collection pressure is to use object pooling. Object pooling is a technique where objects are returned to a shared poll after they are no longer needed, instead of being deallocated. This can reduce the amount of memory that needs to be allocated or deallocated, which can reduce garbage collection pressure.

Wikipedia writes:

> Object pooling can offer a significant performance boost in situations where the cost of initializing a class instance is high and the rate of instantiation and destruction of a class is high – in this case objects can frequently be reused, and each reuse saves a significant amount of time.

## Implementation

In Go, the `sync.Pool` package provides a simple way to implement object pooling. The `sync.Pool` type is a cache of objects that can be shared between multiple goroutines. When an object is no longer needed, it can be returned to the pool instead of being deallocated.

Here is an example of how to use `sync.Pool`:

```go
package main

import (
  "fmt"
  "sync"
)

type Object struct {
  value int
}

func main() {
  pool := sync.Pool{
    New: func() interface{} {
      return &Object{}
    },
  }

  // get an object from the pool, create a new one if the pool is empty
  obj := pool.Get().(*Object)

  fmt.Println(obj.value) // prints 0

  obj.value = 42
  fmt.Println(obj.value) // prints 42

  pool.Put(obj) // the object is now in the pool and can be reused

  obj = pool.Get().(*Object)
  fmt.Println(obj.value) // prints 42, indicating that the object was reused
}
```

In this example, we create a `sync.Pool` with a `New` function that returns a new `Object` instance. We then get an object from the pool using the `Get` method, set its value to 42, and print it. Finally, we return the object to the pool using the `Put` method. When we get an object from the pool again, we can see that it has the same value as before, indicating that it was reused.

This is a silly example, but it demonstrates simply how `sync.Pool` can be used. The `sync.Pool` package is designed to be thread safe, so it can be safely shared between multiple goroutines.

## Further Reading

### For those interested in how program memory fits into the larger picture.

![OS Memory](/assets/img/posts/2024-09-03-reducing-gc-pressure-with-in-go/os-mem.png){: .light}
![OS Memory](/assets/img/posts/2024-09-03-reducing-gc-pressure-with-in-go/os-mem-dark.png){: .dark}
_Memory layout in the OS_

In the operating system level, each process (represeing a running program) has its own memory space. Each process has its own memory space, which is divided into several segments like the stack, heap, text, and data segments.

These segments are tracked in the Process Control Block (PCB) of the process.

## References

1. [https://en.wikipedia.org/wiki/Object_pool_pattern](https://en.wikipedia.org/wiki/Object_pool_pattern)
