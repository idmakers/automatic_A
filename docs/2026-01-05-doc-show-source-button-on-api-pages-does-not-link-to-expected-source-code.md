---
title: "Show Source" Button on API Pages Does Not Link to Expected Source Code
tags:
  - pandas
  - documentation
  - api

# Show Source Button on API Pages Does Not Link to Expected Source Code

## Core Problem
The "Show Source" button on the new version of the documentation pages does not link to the expected source code for the relevant APIs, causing confusion and unnecessary clicks.

## Solution & Analysis
### Current Behavior
The current implementation shows the source code for the current documentation page (.e.g. https://pandas.pydata.org/docs/_sources/reference/api/pandas.DataFrame.rst.txt) instead of the intended API source code.

```python
# Example: pandas.DataFrame source code link
print(pandas.DataFrame._get_source__)
```

### Suggested Fix
To address this issue, we propose removing the "Show Source" button or remapping it to the same link as the current "[source]" link. If the latter is chosen, the current "[source]" link should be removed.

```python
# Example: pandas.DataFrame source code with [source] link
print("View source:")
print("[source]")
```

### Rationale
The suggested fix aims to improve user experience by reducing confusion and unnecessary clicks. By linking the "Show Source" button to the same location as the current "[source]" link, we ensure that users can easily access the relevant API documentation.

## Conclusion
By implementing the suggested fix, we can enhance the usability of our API documentation pages and provide a better experience for users. This change will also align with best practices for documenting APIs and reducing unnecessary complexity.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/48715)