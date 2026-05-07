---
title : "Weapons to have in your STL Arsenal for Competitive Programming"
description : "C++ is preffered language for competitive programming not just because it is 🔥 blazing fast but also because of its rich STL library. This article highlights the most important utilities of the STL for competitive programming."
date : 2026-05-08
subject : "C++"
order : 2
---

These Standard Template Library (STL) is a mixture of algorithms and data structures that are implemented in C++. The key to master them is to understand :

1. What problems they solve
2. What assumptions they need
3. What they actually do under the hood

## `std::sort`
```cpp
std::sort(pointer_to_first_element, pointer_to_one_past_last_element , comparator_function(optional));
```

- `pointer_to_first_element` : is the pointer to the first element of the range to be sorted.
- `pointer_to_one_past_last_element` : is the pointer to one past the last element of the range to be sorted. This means that the sorting will include all elements from the first pointer up to, but not including, the second pointer.
- `comparator_function` (optional) : is a function that defines the sorting criteria. If not provided, the default sorting order (ascending) will be used.
- The `std::sort` function is a highly efficient sorting algorithm that typically implements a hybrid of QuickSort, HeapSort, and Insertion Sort, depending on the size of the input and the characteristics of the data. It is designed to be fast and efficient for a wide range of sorting tasks. 
- Time complexity : `O(n log n)` on average and `O(n^2)` in __the worst case__ (when the input is already sorted or nearly sorted). However, the implementation of `std::sort` is optimized to avoid this worst-case scenario, making it efficient for most sorting tasks.

## `std::lower_bound` 
```cpp
std::lower_bound(pointer_to_first_element, pointer_to_one_past_last_element, value_to_find);
// vector must be sorted for this to work correctly
```
- `pointer_to_first_element` : is the pointer to the first element of the sorted range in which to search for the value.
- `pointer_to_one_past_last_element` : is the pointer to one past the last element of the sorted range in which to search for the value. This means that the search will include all elements from the first pointer up to, but not including, the second pointer.
- `value_to_find` : is the value for which to search in the sorted range.
> Note : Finds first element >= x in the sorted range and __returns an iterator to it__. If all elements in the range are less than `value_to_find`, it returns a pointer to one past the last element of the range.
- Time complexity : `O(log n)` where `n` is the number of elements.

```cpp
vector<int> v = {1,2,4,4,4,7,9};

auto it = lower_bound(v.begin(), v.end(), 4);
```

Points to : `4` (the first occurrence of `4` in the vector)

### Visualization

Index:  0 1 2 3 4 5 6

Value:  1 2 `4` 4 4 7 9

### If elements are less than `value_to_find`
```cpp   
auto it = lower_bound(v.begin(), v.end(), 10);
```
Points to : `v.end()` (one past the last element of the vector)

### If element Not Present

```cpp
lower_bound(v.begin(), v.end(), 5);
```
Points to : `7` (the first element __greater than equal to__ `5` in the vector)

### Index from iterators

`lower_bound` and `upper_bound` return an iterator, to get the index we can do :
```cpp
int index = it - v.begin(); // here it is the iterator returned by lower_bound or upper_bound
```

### Count smaller Elements
To count the number of elements smaller than a given value `x` in a sorted vector.
```cpp
int count_smaller = lower_bound(v.begin(), v.end(), x) - v.begin();
```
This works because `lower_bound` returns an iterator to the first element that is greater than or equal to `x`. By subtracting the beginning of the vector from this iterator, we get the number of elements that are smaller than `x`.

## `std::upper_bound`
```cpp
std::upper_bound(pointer_to_first_element, pointer_to_one_past_last_element, value_to_find);
// vector must be sorted for this to work correctly
```
- `pointer_to_first_element` : is the pointer to the first element of the sorted range
- `pointer_to_one_past_last_element` : is the pointer to one past the last element of the sorted range
- `value_to_find` : is the value for which to search in the sorted range.
> Note : Finds first element > x in the sorted range and __returns an iterator to it__. If all elements in the range are less than or equal to `value_to_find`, it returns a pointer to one past the last element of the range.
- Time complexity : `O(log n)` where `n` is the number of elements.

### Tricks with `lower_bound` and `upper_bound`
To count the number of occurrences of a value `x` in a sorted vector `v` :
```cpp
int count_occurrences = upper_bound(v.begin(), v.end(), x) - lower_bound(v.begin(), v.end(), x);
```
This works because `upper_bound` returns an iterator to the first element that is greater than `x`, while `lower_bound` returns an iterator to the first element that is greater than or equal to `x`. By subtracting the two iterators, we get the number of occurrences of `x` in the vector. 


## `std::nth_element`
```cpp
std::nth_element(pointer_to_first_element, pointer_to_nth_element, pointer_to_one_past_last_element, comparator_function(optional));
```
- `pointer_to_first_element` : is the pointer to the first element of the range.
- `pointer_to_nth_element` : is the pointer to the nth element in the range that we want to find. After the function call, this element will be in its final sorted position.
- `pointer_to_one_past_last_element` : is the pointer to one past the last element of the range.
- `comparator_function` (optional) : is a function that defines the sorting criteria. If not provided, the default sorting order (ascending) will be used.
- The `std::nth_element` function rearranges the elements in the range such that the element at the nth position is the element that would be in that position if the range were fully sorted. The elements before the nth element are less than or equal to it, and the elements after the nth element are greater than or equal to it. However, the order of the elements before and after the nth element is not guaranteed to be sorted.
- Time complexity : `O(n)` on average, but it can degrade to `O(n^2)` in the worst case (when the input is already sorted or nearly sorted). However, the implementation of `std::nth_element` is optimized to avoid this worst-case scenario, making it efficient for most tasks.

### Example
```cpp
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
int n = 5; // We want to find the 5th smallest element
std::nth_element(v.begin(), v.begin() + n - 1, v.end());
cout << "The 5th smallest element is: " << v[n - 1] << endl;
```

## `std::unique`
```cpp
std::unique(pointer_to_first_element, pointer_to_one_past_last_element, binary_predicate_function(optional));
```
- `pointer_to_first_element` : is the pointer to the first element of the range.
- `pointer_to_one_past_last_element` : is the pointer to one past the last element of the range.
- `binary_predicate_function` (optional) : is a function that defines the criteria for uniqueness. If not provided, the default is to consider two elements as duplicates if they are equal.
- The `std::unique` function __removes consecutive duplicate elements from the range__. It does this by comparing each element with the next one and only keeping the first occurrence of each group of duplicates. After the function call, the range will contain only unique elements, but the order of the remaining elements is not guaranteed to be the same as the original order. __The function returns an iterator to the new end of the range, which can be used to erase the "removed" elements from the container__.
- Time complexity : `O(n)` where `n` is the number of elements in the range.

## `std::reverse`
```cpp
std::reverse(pointer_to_first_element, pointer_to_one_past_last_element);
```

reverse the order of elements in the range defined by the two pointers. The first pointer points to the first element of the range, and the second pointer points to one past the last element of the range. After the function call, the elements in the range will be reversed in place.
- Time complexity : `O(n)` where `n` is the number of elements in the range.

Example
```cpp
vector<int> v = {1,2,3,4};

reverse(v.begin(), v.end());
```
Result:
```
4 3 2 1
```

## `std::next_permutation`
```cpp
std::next_permutation(pointer_to_first_element, pointer_to_one_past_last_element, comparator_function(optional));
```


- `pointer_to_first_element` : is the pointer to the first element of the range.
- `pointer_to_one_past_last_element` : is the pointer to one past the last
element of the range.
- `comparator_function` (optional) : is a function that defines the sorting criteria. If not provided, the default sorting order (ascending) will be used.
- The `std::next_permutation` function generates the next lexicographically greater permutation of the elements in the range. __If the function can rearrange the elements to form a new permutation, it returns true__. If the function cannot rearrange the elements to form a new permutation (i.e., __the current permutation is the last permutation in lexicographical order__), it rearranges the elements to the first permutation (the smallest lexicographical order) and returns false.
- Time complexity : `O(n)` where `n` is the number of elements in the range.

 > What “Lexicographical” Means __Dictionary order__. Like words: 123 < 132 < 213 < 231 < 312 < 321

 ```cpp
 vector<int> v = {1,2,3};

do {
    for(int x : v)
        cout << x;

    cout << '\n';

} while(next_permutation(v.begin(), v.end()));
 ```
    Output:
    ```
    123
132
213
231
312
321
 ```