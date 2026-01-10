---
title: "Shared Mutable State in Pandas Index Labels"
tags:
  - pandas
  - copy-on-write
  - data manipulation

---

# Shared Mutable State in Pandas Index Labels

When using the `copy_on_write` feature in pandas, a common issue arises with shared mutable state across Series and DataFrame (and Index). This can lead to unexpected behavior and inconsistencies.

## Core Problem

When you enable `copy_on_write`, you might expect that index labels are treated independently between Series and DataFrames. However, this is not the case. The mutated index label still shares mutable state with both the original DataFrame and any subsequent Series created from it.

## Solution & Analysis

To demonstrate this issue, let's create a simple example:

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

## This Series shares an Index object with DataFrame

```python
s1 = df["a"]
print(s1)
```

    0     1
    1     2
    2     3
   Name: a, dtype: int64


## Mutating a Series breaks the link to the DataFrame index

```python
s2 = df["a"]
s2.loc[1] = 100
print(s2)
```

    0      1
    1   100
    2      3
   Name: a, dtype: int64


## Question: Should this Index object be treated independently from DataFrame or is it okay that a mutation here affects the DataFrame?

```python
i = df.index
i.name = "new name"
print(df.index.name)
```

    new name


## This Series hasn't been mutated. It shares an Index object with DataFrame.

```python
print(s1.index.name)
```

    new name

## This Series has been mutated. It doesn't share an Index object with DataFrame.

```python
print(s2.index.name)
```

    None


```python
s1.loc[1] = 100
```

## Conclusion

When using `copy_on_write` in pandas, it's essential to understand how index labels are shared between Series and DataFrames. This can lead to unexpected behavior if not handled correctly. By being aware of this issue, you can take steps to ensure your data manipulation code produces the expected results.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/53529)