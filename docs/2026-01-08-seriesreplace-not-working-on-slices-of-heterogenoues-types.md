---
title: "Series.replace not working on slices of heterogeneous types"
tags:
  - pandas
  - series
  - replace

---

# Series.replace not working on slices of heterogeneous types

## Core Problem

The `Series.replace` function in pandas does not work as expected when applied to a slice of a heterogeneous type series. This issue can lead to unexpected behavior and incorrect results.

## Solution & Analysis

To understand this issue, let's first look at the code sample provided:

```python
import pandas as pd
import numpy as np 

c = pd.Series([
    np.nan,
    1,
    "hello",
])
c_replaced_1 = c[0:3].replace({np.nan: None})
c_replaced_2 = c[0:2].replace({np.nan: None})
print(c_replaced_1)
print(c_replaced_2)
```

As expected, `c_replaced_1` prints:

```python
0     None
1        1
2    hello
dtype: object
```

However, `c_replaced_2` does not print as expected, instead it prints:

```python
0    NaN
1    1.0
dtype: float64
```

The problem here is that when we apply `replace` to a slice of the series, pandas tries to replace each value in the slice separately. However, if the values in the slice are of different types (as seen in `c_replaced_2`), this can lead to unexpected behavior.

To fix this issue, you can use the `inplace=True` parameter when applying `replace` to a slice of the series. Here's how you can modify the code:

```python
import pandas as pd
import numpy as np 

c = pd.Series([
    np.nan,
    1,
    "hello",
])
c_replaced_1 = c[0:3].replace({np.nan: None}, inplace=True)
c_replaced_2 = c[0:2].replace({np.nan: None}, inplace=True)
print(c_replaced_1)
print(c_replaced_2)
```

Alternatively, you can convert the series to a homogeneous type before applying `replace`. Here's how you can do it:

```python
import pandas as pd
import numpy as np 

c = pd.Series([
    np.nan,
    1,
    "hello",
])
# Convert the series to float64 before applying replace
c_float = c.astype(float)
c_replaced_2 = c_float[0:2].replace({np.nan: None})
print(c_replaced_2)
```

## Conclusion

The `Series.replace` function in pandas can be finicky when applied to slices of heterogeneous type series. By using the `inplace=True` parameter or converting the series to a homogeneous type, you can avoid this issue and ensure that your code produces the expected results.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/29813)