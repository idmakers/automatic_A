---
title: "Pandas timedelta64[s] Series Constructor Inequality"
tags:
  - pandas
  - datetime
  - timedelta

---

# The Issue with timedelta64[s] Series Constructor

## Core Problem

The `timedelta64[s]` series constructor in pandas seems to ignore the seconds and interpret it as nanoseconds, leading to an inequality when compared to the alternative constructor using `to_timedelta` with unit `'s'`.

## Solution & Analysis

### Reproducible Example

```python
import pandas as pd
from pandas import Series
import pandas._testing as tm
import numpy as np

result = Series([1000000, 200000, 3000000], dtype="timedelta64[s]")
expected = Series(pd.to_timedelta([1000000, 200000, 3000000], unit="s"))

# This code passes for timedelta64[ns]
result_ns = Series([1000000, 200000, 3000000], dtype="timedelta64[ns]")
expected_ns = Series(pd.to_timedelta([1000000, 200000, 3000000], unit="ns"))
tm.assert_series_equal(result_ns, expected_ns)

# But this code fails for timedelta64[s]
result_fail = np.array([1000000, 200000, 3000000], dtype="timedelta64[s]")
result_pandas = pd.Series([1000000, 200000, 3000000], dtype="timedelta64[s]")
tm.assert_numpy_array_equal(result_fail, result_pandas.values)
```

### Explanation

The issue arises from the way pandas handles time units in the `timedelta64` constructor. When using `dtype="timedelta64[ns]"`, pandas correctly interprets nanoseconds. However, when using `dtype="timedelta64[s]"`, pandas seems to interpret seconds as nanoseconds instead.

To resolve this issue, we can use the alternative constructor with `to_timedelta` and specify the unit `'s'`. This ensures that pandas correctly interprets the time units.

### Conclusion

The `timedelta64[s]` series constructor in pandas should be updated to correctly handle seconds. Until then, using the alternative constructor with `to_timedelta` and specifying the unit `'s'` provides a reliable workaround.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/48312)