---
title: "Pandas Timedelta64 Series Constructor Issue with Unit 's'"
tags:
  - pandas
  - timedelta64
  - series constructor

---

# Pandas Timedelta64 Series Constructor Issue with Unit 's'

## Core Problem

When creating a `timedelta64[s]` series in pandas, the constructor does not behave as expected when using the unit 's'. This results in an unexpected conversion to nanoseconds instead of seconds.

## Solution & Analysis

To reproduce the issue:

```python
import pandas as pd
from pandas import Series
import pandas._testing as tm
import numpy as np

result = Series([1000000, 200000, 3000000], dtype="timedelta64[s]")
expected = Series(pd.to_timedelta([1000000, 200000, 3000000], unit="s"))
tm.assert_series_equal(result, expected)
```

However, when using `dtype="timedelta64[ns]"` and `unit="ns"`:

```python
result = np.array([1000000, 200000, 3000000], dtype="timedelta64[ns]")
result_pandas = pd.Series([1000000, 200000, 3000000], dtype="timedelta64[ns]")
tm.assert_numpy_array_equal(result, result_pandas.values)
```

This should pass, but we seem to ignore the seconds and interpret it as nanoseconds. The issue arises because of a misunderstanding in the conversion between the unit 's' (seconds) and 'ns' (nanoseconds).

To resolve this issue, you need to ensure that the correct units are used when creating the `timedelta64` series.

## Conclusion

In conclusion, the pandas `timedelta64` series constructor has an issue with interpreting seconds as nanoseconds when using the unit 's'. To fix this, use the correct units in your code.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/48312)