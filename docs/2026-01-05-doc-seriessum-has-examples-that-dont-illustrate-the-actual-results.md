---
title: "Pandas Series.sum() Examples Fail to Illustrate Actual Results"
tags:
  - pandas
  - series
  - sum
---

# Pandas Series.sum() Examples Fail to Illustrate Actual Results

## Core Problem

The `Series.sum()` function in the Pandas library has examples that do not accurately represent the actual results. These examples are hardcoded and do not execute the code, making it difficult for users to understand the behavior of the function.

## Solution & Analysis

### Current Issue

The current example in the documentation:
```python
>>> idx = pd.MultiIndex.from_arrays(
...     [["warm", "warm", "cold", "cold"], ["dog", "falcon", "fish", "spider"]],
...     names=["blooded", "animal"],
... )
>>> s = pd.Series([4, 2, 0, 8], name="legs", index=idx)
>>> s.sum()
```
Indicates that `s.sum()` is 14. However, at runtime, it returns an `np.int64`:
```python
>>> s.sum()
np.int64(14)
```
The printed result depends on the backend.

### Proposed Fix

One possible solution to this issue is to make the examples executeable by default, rather than having hardcoded results. This would allow users to see the actual behavior of the function.

Another option could be to indicate whether the returned type is `np.int64` or a Python integer, depending on the context and backend used.

### Code Example

Here's an example of how the documentation could be updated to make the examples executeable:
```python
>>> idx = pd.MultiIndex.from_arrays(
...     [["warm", "warm", "cold", "cold"], ["dog", "falcon", "fish", "spider"]],
...     names=["blooded", "animal"],
... )
>>> s = pd.Series([4, 2, 0, 8], name="legs", index=idx)
result = s.sum()
print(result)  # Output: 14
```
In this example, the `result` variable is assigned the output of `s.sum()` and then printed to show the actual result.

## Conclusion

The current examples in the Pandas documentation for `Series.sum()` do not accurately represent the actual results. By making the examples executeable or indicating the returned type, we can improve the accuracy and usefulness of the documentation.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/62966)