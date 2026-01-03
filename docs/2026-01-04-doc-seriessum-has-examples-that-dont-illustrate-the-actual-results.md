---
title: "Pandas Series.sum() Misrepresentation in Documentation"
tags:
  - pandas
  - series
  - sum
---

# [Use the Title]

## Core Problem
The `Series.sum()` function in pandas has examples that don't accurately illustrate its actual results. The documentation provides hardcoded values, which can lead to confusion about the behavior of this function.

## Solution & Analysis
To fix this issue, we need to rethink how the examples are created and presented in the documentation. There are two possible approaches:

### Approach 1: Execute Code for Results

Instead of providing hardcoded results, we could modify the documentation to execute the code and display the actual output. This would ensure that the examples accurately reflect the behavior of the function.

```python
>>> import pandas as pd

idx = pd.MultiIndex.from_arrays(
    [["warm", "warm", "cold", "cold"], ["dog", "falcon", "fish", "spider"]],
    names=["blooded", "animal"],
)
s = pd.Series([4, 2, 0, 8], name="legs", index=idx)

# Execute the code and display the result
result = s.sum()
print(result)  # Output: 14
```

### Approach 2: Indicate Numpy Types

Another option is to indicate whether the function returns numpy types or python types. This would allow users to understand the behavior of the function and decide how to use it accordingly.

```python
>>> import pandas as pd

idx = pd.MultiIndex.from_arrays(
    [["warm", "warm", "cold", "cold"], ["dog", "falcon", "fish", "spider"]],
    names=["blooded", "animal"],
)
s = pd.Series([4, 2, 0, 8], name="legs", index=idx)

# Display the type of result
result_type = s.sum().dtype
print(result_type)  # Output: int64

# Execute the code and display the result
if result_type == 'int64':
    print(s.sum())  # Output: 14
```

## Conclusion
To ensure that the documentation for `Series.sum()` is accurate, we should consider modifying the examples to execute the code and display the actual output. Alternatively, we can indicate the type of result returned by the function, allowing users to understand its behavior.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/62966)