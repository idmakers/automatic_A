---
title: "The Mysterious Case of the Missing Source Link"
tags:
  - pandas
  - documentation
  - API

---

# The Mysterious Case of the Missing Source Link

## Core Problem

The "Show Source" button on recent versions of the Pandas API documentation pages does not link to the expected source code, causing confusion among users. This issue affects the usability and accuracy of the documentation.

## Solution & Analysis

### Problematic Code

```python
# Example of problematic code
from pandas import DataFrame

def create_dataframe(data):
    return DataFrame(data)
```

In this example, the "Show Source" button would link to a different file than expected, leading to incorrect information.

### Proposed Fix

To address this issue, we can remove the "Show Source" button or remap it to the same link as the current "[source]" link. For developers who want to access the documentation source, we can move the link to a less prominent area and rename it to "Show Documentation Source".

```python
# Proposed fix
from pandas import DataFrame

def create_dataframe(data):
    # Return a new DataFrame instance
    return DataFrame(data)
```

## Conclusion

The current implementation of the "Show Source" button on Pandas API documentation pages is confusing and inaccurate. By removing or remapping this link, we can improve the usability and accuracy of the documentation for both users and developers.

### Future Work

To further address this issue, we can:

* Move the "Show Documentation Source" link to a less prominent area, such as the footer or sidebar.
* Implement a feature to automatically generate documentation source links based on the API documentation structure.

By addressing this issue, we can provide better support for users and developers of the Pandas library.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/48715)