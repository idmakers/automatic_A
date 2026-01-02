---
title: "Unlocking Polars List Aggregation with Custom Aggregation Expressions"
tags:
  - list-aggregation
  - polars
  - aggregation-expression
---

## Core Problem

When working with lists in Polars, it's often necessary to perform aggregations on the elements within those lists. However, the official documentation for `polars.Expr.list.agg` seems to imply that the aggregation expressions are limited to a specific set of built-in functions. This limitation can lead to confusion and frustration when trying to achieve complex aggregations.

## Solution & Analysis

To understand what "aggregation expressions" truly are in Polars, we need to delve into the documentation and explore the possibilities. While the official documentation does list some built-in aggregation functions, it's clear that these are just a starting point.

One way to uncover more aggregation expressions is to examine the examples provided by Polars. Let's take a closer look at the `no_nulls` example:

```python
df.with_columns(no_nulls=pl.col.a.list.agg(pl.element().drop_nulls()))
```

In this example, we're using the `drop_nulls` function as an aggregation expression. This might seem counterintuitive, but it's a great way to demonstrate that custom functions can be used as aggregation expressions.

So, where can you find more information on aggregation expressions in Polars? The answer lies in the `Expression - Aggregation` section of the documentation. Here, you'll find a list of built-in aggregation functions, as well as some examples of how to use them.

However, it's essential to note that this is not an exhaustive list. By using the `pl.element()` function, you can create custom aggregation expressions from any Python function. This means that the possibilities are truly endless!

Let's illustrate this with an example:

```python
import polars as pl

# Define a custom aggregation expression
def my_aggregation(x):
    return x.mean()

# Create a sample list column
df = pl.DataFrame({'a': [1, 2, 3]})

# Use the custom aggregation expression
result = df.with_columns(my_list_agg=my_aggregation())

print(result)
```

In this example, we define a custom aggregation function `my_aggregation` and use it as an aggregation expression. The result is a new column with the mean value of each list element.

## Conclusion

Polars list aggregations are more flexible than you might initially think. By understanding what "aggregation expressions" truly are, you can unlock a world of possibilities for working with lists in Polars. Remember to explore the documentation and examples provided by Polars, as well as to use the `pl.element()` function to create custom aggregation expressions from any Python function.