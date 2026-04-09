---
title: "Python Decorators"
description: "Understanding decorators in Python — from basic syntax to advanced patterns with arguments, stacking, and class-based decorators."
date: 2025-01-20
subject: "Python"
order: 1
---

## What Is a Decorator?

A decorator is a function that takes another function as an argument, extends its behavior, and returns a new function — all without modifying the original function's source code.

In Python, decorators use the `@` syntax, which is syntactic sugar for passing a function into another function:

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"{func.__name__} returned {result}")
        return result
    return wrapper

@log_call
def add(a, b):
    return a + b

add(2, 3)
# Output:
# Calling add
# add returned 5
```

The `@log_call` line is equivalent to writing `add = log_call(add)`.

## Why Use Decorators?

Decorators embody the **open/closed principle**: you can extend behavior without modifying existing code. Common use cases include:

- **Logging** function calls and return values
- **Authentication** checks before API endpoints
- **Caching** expensive computations
- **Rate limiting** requests
- **Input validation**
- **Retry logic** for unreliable operations

### Preserving Function Metadata

A common issue with decorators is that they obscure the original function's metadata. The `functools.wraps` decorator solves this:

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def greet(name):
    """Return a greeting message."""
    return f"Hello, {name}!"

print(greet.__name__)  # "greet" (not "wrapper")
print(greet.__doc__)   # "Return a greeting message."
```

## Decorators with Arguments

Sometimes you need a decorator that accepts its own parameters. This requires an additional layer of nesting:

```python
def repeat(n):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(n):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def say_hello():
    print("Hello!")

say_hello()
# Output: Hello! (printed 3 times)
```

### Timing Decorator

A practical example — measuring function execution time:

```python
import time
from functools import wraps

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed = time.perf_counter() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)
    return "done"
```

## Stacking Decorators

Multiple decorators can be applied to a single function. They execute from **bottom to top**:

```python
@log_call
@timer
def compute(x):
    return x ** 2
```

This is equivalent to `compute = log_call(timer(compute))`.

## Class-Based Decorators

You can also implement decorators as classes using the `__call__` method:

```python
class CountCalls:
    def __init__(self, func):
        self.func = func
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"Call #{self.count} to {self.func.__name__}")
        return self.func(*args, **kwargs)

@CountCalls
def greet(name):
    return f"Hello, {name}!"

greet("Alice")  # Call #1 to greet
greet("Bob")    # Call #2 to greet
```

## Summary

Decorators are one of Python's most elegant features. They provide a clean, reusable way to extend function behavior, keeping code DRY and adhering to separation of concerns. Whether you need logging, caching, or access control, decorators offer a composable solution.
