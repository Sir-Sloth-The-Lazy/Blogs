---
title: "Python Generators"
description: "Learn how generators work in Python — lazy evaluation, memory efficiency, and building data pipelines with yield."
date: 2025-02-05
subject: "Python"
order: 2
---

## What Are Generators?

Generators are a special type of iterable in Python. Unlike lists, which store all elements in memory, generators produce values **lazily** — one at a time, on demand. This makes them incredibly memory-efficient for working with large datasets.

A generator function uses the `yield` keyword instead of `return`:

```python
def count_up_to(n):
    i = 1
    while i <= n:
        yield i
        i += 1

for num in count_up_to(5):
    print(num)
# Output: 1, 2, 3, 4, 5
```

When a generator function is called, it returns a **generator object** without executing the function body. The body runs only when values are requested via iteration.

## How Yield Works

The `yield` keyword pauses the function's execution and sends a value back to the caller. When the next value is requested, execution resumes from where it left off:

```python
def simple_gen():
    print("Step 1")
    yield 1
    print("Step 2")
    yield 2
    print("Step 3")
    yield 3

gen = simple_gen()
print(next(gen))  # Prints "Step 1", returns 1
print(next(gen))  # Prints "Step 2", returns 2
print(next(gen))  # Prints "Step 3", returns 3
```

This pause-and-resume mechanism is what makes generators fundamentally different from regular functions.

## Memory Efficiency

Consider reading a large file line by line. A list-based approach loads everything into memory:

```python
# Bad: loads entire file into memory
def read_all_lines(filename):
    with open(filename) as f:
        return f.readlines()
```

A generator-based approach processes one line at a time:

```python
# Good: constant memory usage
def read_lines(filename):
    with open(filename) as f:
        for line in f:
            yield line.strip()
```

For a 10GB log file, the generator approach uses essentially constant memory regardless of file size.

## Generator Expressions

Just as list comprehensions create lists, **generator expressions** create generators using parentheses:

```python
# List comprehension — creates all values in memory
squares_list = [x**2 for x in range(1_000_000)]

# Generator expression — creates values on demand
squares_gen = (x**2 for x in range(1_000_000))

# Memory comparison
import sys
print(sys.getsizeof(squares_list))  # ~8 MB
print(sys.getsizeof(squares_gen))   # ~200 bytes
```

### Chaining with Pipelines

Generator expressions compose naturally into data processing pipelines:

```python
lines = read_lines("access.log")
errors = (line for line in lines if "ERROR" in line)
messages = (line.split(": ", 1)[1] for line in errors)
unique = set(messages)
```

Each step processes data lazily — no intermediate lists are created.

## Advanced Patterns

### Infinite Sequences

Generators can represent infinite sequences because they only produce values on demand:

```python
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

from itertools import islice
first_ten = list(islice(fibonacci(), 10))
# [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

### Sending Values with send()

Generators can receive values through the `send()` method, enabling two-way communication:

```python
def accumulator():
    total = 0
    while True:
        value = yield total
        if value is not None:
            total += value

acc = accumulator()
next(acc)        # Initialize — returns 0
acc.send(10)     # Returns 10
acc.send(20)     # Returns 30
acc.send(5)      # Returns 35
```

### yield from

The `yield from` syntax delegates to another generator, simplifying nested iteration:

```python
def flatten(nested):
    for item in nested:
        if isinstance(item, list):
            yield from flatten(item)
        else:
            yield item

list(flatten([1, [2, [3, 4], 5], 6]))
# [1, 2, 3, 4, 5, 6]
```

## Summary

Generators are one of Python's most powerful features for writing memory-efficient, composable code. By producing values lazily, they enable processing of datasets that wouldn't fit in memory, and their composability makes them ideal for building clean data pipelines.
