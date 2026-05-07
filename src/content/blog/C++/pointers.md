---
title : "Utilities of Pointers for Competitive Programming"
description : "A comprehensive guide to understanding and utilizing pointers in C++ for competitive programming, including practical examples and best practices. "
date : 2026-05-07
subject : "C++"
order : 1
---

# Pointers and Why do we need it ?
A pointer stores the memory address of a variable.

```cpp
int x = 10 ; // variable x stores the value 10
int *ptr = &x; // ptr stores the address of x
```

- `x`  is the variable
- `ptr` is a pointer that holds the address of `x`
- `&` is the `address-of operator`, which gives the memory address of `x`
- `*` is the `dereference operator`, which allows us to access the value stored at the memory address that `ptr` points to.

Memory view : 
```
x = 10 
ptr = 0x1011
```

### Important Distinction to know when you set out
```cpp
int *p ;
```
means : 
> `p` is a pointer to an integer. It can hold the address of an integer variable.

__NOT__ : 
> `p` is an integer variable that can hold a pointer value.

_So_, The `*` belongs to the varible and not to the type.

__This might surprise you but the following is also valid__
```cpp
int* p, b;
```
means : 

p &rArr; is an `integer pointer` variable

b &rArr; is an `integer` variable

# Two Different Meaning of `*` in C++
The `*` symbol in C++ has two distinct meanings based on the context in which it is used:

1. **Pointer Declaration**: When used in a variable declaration, the `*` indicates that the variable being declared is a pointer to a specific type. For example:
   ```cpp
   int* ptr; // ptr is a pointer to an integer
   ```
   In this context, `ptr` is declared as a pointer to an integer type.
2. **Dereferencing Operator**: When used in an expression, the `*` operator is used to dereference a pointer, meaning it accesses the value stored at the memory address that the pointer points to. For example:
   ```cpp
   int x = 10;
   int* ptr = &x; // ptr points to x
   int value = *ptr; // value is now 10, the value at the address that ptr points to
   ```
In this context, `*ptr` retrieves the value stored at the memory address that `ptr` is pointing to.

#  Size does not matter

Well it does but not in the way you think :smiley: , in C++, __the size of a pointer does not change regardless of the type of variable it points to__, simply because a pointer is just an address holder, but they might vary based on the architecture of the system.

# I did not like arithmetic (pointer are not arithmetic)

I was more of a guy who liked finding x rather than numbers , well sorry for digressing a bit :sweat_smile: , however, pointer arithmetic is quite useful , you should believe it coming from me!.

```cpp
int arr[] = {10, 20, 30};
int* ptr = arr; // ptr points to the first element of arr
std::cout << *ptr << std::endl; // Output: 10
ptr++; // Move the pointer to the next element
std::cout << *ptr << std::endl; // Output: 20
```

From the above example the takeaway is that :

1. `ptr++` or `ptr+1` moves the pointer to the next element in the array, not just to the next memory address. This is because the pointer arithmetic takes into account the size of the data type it points to (in this case, `int` `ptr+1` &rArr;  if `p=1000` and `sizeof(int) = 4`, then `ptr+1` will be 1004).
2. This only works for contigous memory locations, such as arrays. If you have a pointer that points to a single variable, incrementing it would lead to undefined behavior, __also maps, linked lists, heaps etc. are not contigous memory locations so pointer arithmetic does not apply to them__.