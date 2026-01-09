---
title: "Copy-on-Write (CoW) in Pandas: Index Labels as Shared Mutable State"
tags:
  - pandas
  - copy-on-write
  - index labels

---

# Copy-on-Write (CoW) in Pandas: Index Labels as Shared Mutable State

When turning on Copy-on-Write mode, a notable issue arises with the shared mutable state of index labels across Series and DataFrame. This problem affects the coupling between DataFrame index and Series index.

## Core Problem

In pandas 2.0.2, when Copy-on-Write (CoW) is enabled, index labels are still shared as mutable state. Specifically, this means that once a Series is constructed, its index label can be mutated independently of the original DataFrame's index label.

For instance:

```python
import pandas as pd

pd.options.mode.copy_on_write = True

df = pd.DataFrame({"a": [1, 2, 3]})
df
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
    </tr>
  </tbody>
</table>

## Solution & Analysis

The issue arises when we try to create a Series that shares the same index label as the DataFrame:

```python
s = df["a"]
s
```

|   |   |
|---|---|
| 0 | 1 |
| 1 | 2 |
| 2 | 3 |
| Name: a, dtype: int64 |

However, when we try to mutate the index label of this Series, it unexpectedly affects the DataFrame:

```python
i = df.index
i.name = "new name"
print(df.index.name)
```

`new name`

When we print the index label of the Series, we see that it has been updated to `new name`, which is different from the original DataFrame's index label.

This behavior suggests that the index labels are still shared as mutable state when Copy-on-Write mode is enabled.

## Conclusion

In conclusion, the issue with shared mutable state of index labels in pandas 2.0.2 with Copy-on-Write mode enabled highlights the need for careful consideration when working with these data structures. It is essential to understand how changes to one part of a Series or DataFrame affect other parts, especially when working with complex data pipelines.

To address this issue, consider using alternative data structures that do not rely on shared mutable state. Additionally, be mindful of the implications of Copy-on-Write mode and plan accordingly to avoid unexpected behavior in your code.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/53529)