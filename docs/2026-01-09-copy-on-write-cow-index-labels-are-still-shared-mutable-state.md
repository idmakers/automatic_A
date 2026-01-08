---
title: "Copy-on-Write Index Labels Are Still Shared Mutable State"
tags:
  - pandas
  - copy-on-write
  - index labels
---

# Copy-on-Write (CoW): index labels are still shared mutable state

## Core Problem

When turning on Copy-on-Write mode in pandas, index labels remain as shared mutable state across Series and DataFrame. This issue can cause unexpected behavior when working with Series and DataFrames.

## Solution & Analysis

### Reproducible Example

```python
import pandas as pd

pd.options.mode.copy_on_write = True

df = pd.DataFrame({"a": [1, 2, 3]})
print(df)
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

### Mutating a Series breaks the link to the DataFrame index

```python
s1 = df["a"]
print(s1)

s2 = df["a"]
s2.loc[1] = 100
print(s2)
```

    0      1
    1    100
    2      3
    Name: a, dtype: int64

    new name

    new name

### Question: Should this Index object be treated independently from DataFrame or is it okay that a mutation here affects the DataFrame?

```python
i = df.index
i.name = "new name"
print(df.index.name)

s1.loc[1] = 100
```

    new name

    new name

## Conclusion

When using Copy-on-Write mode in pandas, index labels are still shared mutable state across Series and DataFrame. This can cause unexpected behavior when working with Series and DataFrames. To avoid this issue, consider using a different data structure or approach that does not rely on shared mutable state.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/53529)