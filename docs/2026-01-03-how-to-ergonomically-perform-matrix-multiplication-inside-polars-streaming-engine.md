---
title: "Ergonomically Performing Matrix Multiplication Inside Polars Streaming Engine"
tags:
  - python
  - matrix-multiplication
  - polars-engine
---

# Ergonomically Performing Matrix Multiplication Inside Polars Streaming Engine

## Core Problem
When dealing with large datasets, performing matrix multiplication can be a significant challenge due to memory constraints. The problem is exacerbated when the matrices are large or need to be chained together.

## Solution & Analysis
To overcome this issue, we can utilize the streaming engine provided by Polars, which allows for efficient and scalable data processing. We'll create a wrapper function that automates the expansion of linear algebra operations into multiplication and addition.

### Step 1: Define the Matrix

```python
import polars as pl

# Create a sample dataframe with numerical columns
df = pl.DataFrame({
    'c1': [1, 2, 3],
    'c2': [4, 5, 6],
    'c3': [7, 8, 9],
    'v': [10, 11, 12]
})

# Define the matrix M
M = pl.DataFrame({
    'row1': ['c1', 'c2', 'c3'],
    'row2': ['c4', 'c5', 'c6'],
    'row3': ['c7', 'c8', 'c9']
})

# Define the vector v
v = df['v'].alias('v')

# Perform matrix-vector multiplication using Polars streaming engine
result = M.select(*[pl.col(M[col]) * pl.col(v) for col in M.columns]).union(pl.col(v))
```

### Step 2: Automate Linear Algebra Operations

To automate linear algebra operations, we can create a wrapper function that takes in the matrices and vectors as input. This function will handle the expansion of the operations into multiplication and addition.

```python
import polars as pl

def matrix_multiplication(M, v):
    # Select columns for multiplication
    result = M.select(*[pl.col(M[col]) * pl.col(v) for col in M.columns])
    return result.union(pl.col(v))

# Example usage:
M = pl.DataFrame({
    'row1': ['c1', 'c2', 'c3'],
    'row2': ['c4', 'c5', 'c6'],
    'row3': ['c7', 'c8', 'c9']
})

v = pl.DataFrame({
    'v': [10, 11, 12]
})

result = matrix_multiplication(M, v)
```

## Conclusion
By utilizing the Polars streaming engine and creating a wrapper function for linear algebra operations, we can efficiently perform matrix multiplication on large datasets while minimizing memory usage. This approach provides a scalable solution for handling complex data processing tasks.