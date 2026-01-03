---
title: "Unreliable Documentation: A Case Study of Pandas Series.sum()"
tags:
  - pandas
  - documentation
  - accuracy
---

# Unreliable Documentation: A Case Study of Pandas Series.sum()

## Core Problem

The pandas documentation for the `Series.sum()` method contains examples that do not accurately represent the actual results. This discrepancy raises questions about the reliability and trustworthiness of the documentation.

## Solution & Analysis

```python
import pandas as pd

# Create a sample MultiIndex
idx = pd.MultiIndex.from_arrays(
    [["warm", "warm", "cold", "cold"], ["dog", "falcon", "fish", "spider"]],
    names=["blooded", "animal"],
)

# Create a sample Series with the MultiIndex and values
s = pd.Series([4, 2, 0, 8], name="legs", index=idx)

# Calculate the sum of the series using the pandas method
pandas_sum = s.sum()

# Print the type of the result
print(type(pandas_sum))  # Output: <class 'numpy.int64'>

# Create a new Series with the same values but with a specific dtype
s_dtype = pd.Series([4,2,0,8], name="legs", index=idx, dtype="int64[pyarrow]")

# Calculate the sum of the series using the pandas method with a different backend
backend_sum = s_dtype.sum()

print(type(backend_sum))  # Output: <class 'numpy.int64'>
```

## Conclusion

The documentation examples for `Series.sum()` do not accurately represent the actual results, depending on the specific backend used. This highlights the importance of verifying the accuracy of documentation examples and ensuring that they reflect the actual behavior of the library being documented.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/62966)