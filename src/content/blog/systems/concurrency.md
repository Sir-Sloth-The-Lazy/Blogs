---
title: "Concurrency Models"
description: "Exploring concurrency patterns — threads, event loops, actors, and CSP. How modern systems handle parallel execution."
date: 2025-03-15
subject: "Systems"
order: 2
---

## Why Concurrency?

Modern computers have multiple cores, and modern applications must handle thousands of simultaneous connections, user interactions, and background tasks. **Concurrency** is about structuring a program to handle multiple tasks that can overlap in time.

It's important to distinguish concurrency from parallelism:

- **Concurrency**: dealing with many things at once (structure)
- **Parallelism**: doing many things at once (execution)

A concurrent program may run on a single core using time-slicing, while a parallel program requires multiple cores for simultaneous execution.

## Threads

Threads are the most traditional concurrency primitive. A thread is a lightweight unit of execution within a process, sharing the process's memory space.

### Shared State Problems

Because threads share memory, they can read and write the same data simultaneously, leading to:

- **Race conditions**: outcome depends on timing of thread execution
- **Deadlocks**: two threads each waiting for a resource the other holds
- **Data corruption**: partial writes visible to other threads

```python
# Race condition example
counter = 0

def increment():
    global counter
    for _ in range(1_000_000):
        counter += 1  # Not atomic!

# Running two threads may produce counter < 2_000_000
```

### Synchronization Primitives

To manage shared state, operating systems provide:

- **Mutexes**: mutual exclusion locks — only one thread can hold it
- **Semaphores**: counting locks — allow N threads through
- **Condition variables**: allow threads to wait for specific conditions
- **Read-write locks**: multiple readers or one writer

## Event Loops

An alternative to threads is the **event loop** model, popularized by Node.js and used extensively in UI frameworks. A single thread processes events from a queue:

```
while (eventQueue.hasEvents()) {
    event = eventQueue.next();
    handler = lookupHandler(event);
    handler(event);
}
```

### Advantages

- No shared state — each event handler runs to completion
- No locks, no deadlocks, no race conditions
- Excellent for I/O-bound workloads (network servers, UIs)

### Disadvantages

- CPU-bound tasks block the entire loop
- "Callback hell" in early implementations (mitigated by async/await)
- Cannot utilize multiple CPU cores without worker processes

## The Actor Model

The **actor model** (used in Erlang, Akka, and Elixir) treats everything as an independent actor that:

1. Has private state (no shared memory)
2. Communicates via asynchronous messages
3. Can create new actors
4. Can decide how to handle the next message

```elixir
defmodule Counter do
  def start(count \\ 0) do
    receive do
      :increment -> start(count + 1)
      {:get, caller} -> send(caller, count)
                        start(count)
    end
  end
end
```

### Key Benefits

- No shared mutable state — eliminates race conditions by design
- Location transparency — actors can be on different machines
- Fault tolerance — supervisors can restart failed actors
- Natural modeling of real-world concurrent entities

## Communicating Sequential Processes (CSP)

**CSP**, used in Go and Clojure's core.async, models concurrency through independent processes that communicate via **channels**:

```go
func producer(ch chan<- int) {
    for i := 0; i < 10; i++ {
        ch <- i
    }
    close(ch)
}

func consumer(ch <-chan int) {
    for value := range ch {
        fmt.Println(value)
    }
}

func main() {
    ch := make(chan int, 5)
    go producer(ch)
    consumer(ch)
}
```

### CSP vs Actors

| Aspect | CSP | Actors |
|--------|-----|--------|
| Communication | Named channels | Direct messages |
| Identity | Processes are anonymous | Actors have addresses |
| Synchronization | Can be synchronous | Always asynchronous |
| Composition | Channel operations compose | Supervision trees |

## Choosing a Model

The right concurrency model depends on your problem:

- **Threads**: CPU-bound computation, fine-grained parallelism
- **Event loops**: I/O-bound servers, UI applications
- **Actors**: Distributed systems, fault-tolerant services
- **CSP**: Pipeline processing, producer-consumer patterns

## Summary

Concurrency is not a single problem with a single solution. Each model offers different trade-offs between complexity, performance, and safety. Understanding these models helps you choose the right tool for each situation and avoid the subtle bugs that plague concurrent programs.
