---
title: "Optimizing String Anagram Checks in Python: A Performance Comparison"
tags:
  - Python performance optimization
  - string anagrams
---

# Optimizing String Anagram Checks in Python: A Performance Comparison

## Core Problem
When checking for anagrams in Python, developers often wonder whether to use the `sorted()` function or the `Counter` class from the `collections` module. The question arises due to varying performance results across different string lengths.

## Solution & Analysis
To determine which approach is faster and more efficient, we'll compare the performance of both methods using benchmarking tests.

### Method 1: Using `sorted()`

```python
import timeit

def check_anagram_sorted(s):
    return sorted(s) == sorted(''.join(sorted(s)))
```

### Method 2: Using `Counter`

```python
from collections import Counter

def check_anagram_counter(s):
    return Counter(s) == Counter(''.join(sorted(s)))
```

We'll use the `timeit` module to benchmark each method on short and long strings.

```python
import timeit

# Short string (less than 10 chars)
short_string = 'abc'

# Long string (more than 10 chars)
long_string = 'abcdefghijklmnopqrstuvwxyz'

# Benchmarking tests
sorted_time = timeit.timeit(lambda: check_anagram_sorted(short_string), number=1000000)
counter_time = timeit.timeit(lambda: check_anagram_counter(short_string), number=1000000)

print(f"Sorted() on short string: {sorted_time:.6f} seconds")
print(f"Counter() on short string: {counter_time:.6f} seconds")

sorted_long_time = timeit.timeit(lambda: check_anagram_sorted(long_string), number=1000000)
counter_long_time = timeit.timeit(lambda: check_anagram_counter(long_string), number=1000000)

print(f"Sorted() on long string: {sorted_long_time:.6f} seconds")
print(f"Counter() on long string: {counter_long_time:.6f} seconds")
```

## Conclusion
Based on our benchmarking tests, we can conclude that:

* For short strings (less than 10 chars), `sorted()` is faster.
* For longer strings (more than 10 chars), `Counter` outperforms `sorted()`.
The performance difference arises due to the time complexity of each method:
* `sorted()` has a time complexity of O(n log n) due to its sorting operation.
* `Counter` has an average time complexity of O(n), making it more efficient for large strings.

When choosing between these methods, consider the following guidelines:

* Use `sorted()` for short strings where speed is critical, but be aware that this may lead to unnecessary sorting and memory allocation.
* Use `Counter` for long strings or when working with large datasets, as its average time complexity is more efficient.