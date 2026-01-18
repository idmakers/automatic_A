---
title: "Defaulting to Microseconds in Timestamp/Timedelta Creation from Numeric Input"
tags:
  - pandas
  - datetime
  - unit
---

# Defaulting to Microseconds in Timestamp/Timedelta Creation from Numeric Input

## Core Problem

When creating timestamps or timedeltas from numeric input using pandas functions such as `date_range`, `Timestamp`, or `to_datetime` with the `unit` parameter, the resulting dtype often defaults to nanoseconds. However, when the input is in seconds, minutes, hours, etc., it would be more consistent and efficient to return seconds, minutes, hours, etc. instead of nanoseconds.

## Solution & Analysis

One possible solution to this issue is to default to microseconds when creating timestamps or timedeltas from numeric input. This can be achieved by modifying the `to_datetime` function to use the `unit` parameter's value as a multiplier for the microsecond resolution.

```python
import pandas as pd

def to_datetime(values, unit=None):
    # ... ( existing code )
    
    if unit is None:
        # Default to microseconds when possible
        if isinstance(values[0], int) and values[0] > 1e6:
            unit = 'us'
        elif isinstance(values[0], int) and values[0] > 1e3:
            unit = 'ms'
    
    # ... ( existing code )
```

Another possible solution is to return the same dtype as the input when converting from seconds, minutes, hours, etc. This can be achieved by modifying the `to_datetime` function to use a more granular unit of time based on the input value.

```python
import pandas as pd

def to_datetime(values, unit=None):
    # ... ( existing code )
    
    if isinstance(values[0], int) and values[0] > 1e6:
        unit = 'us'
    elif isinstance(values[0], int) and values[0] > 1e3:
        unit = 'ms'
    elif isinstance(values[0], int) and values[0] > 3600:  # hours
        unit = 'h'
    elif isinstance(values[0], int) and values[0] > 60:   # minutes
        unit = 'm'
    
    # ... ( existing code )
```

## Conclusion

Defaulting to microseconds when creating timestamps or timedeltas from numeric input can improve consistency and efficiency. By modifying the `to_datetime` function to use a more granular unit of time based on the input value, we can achieve this while maintaining flexibility and compatibility with different types of input values.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/63275)