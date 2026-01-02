---
title: "Understanding Binary Bytes and Writing to Files in Python"

tags:
  - binary bytes
  - python
  - file writing
  - struct.pack()
---

# Understanding Binary Bytes and Writing to Files in Python

## Core Problem

Converting between binary data and ASCII representations can be confusing. In this article, we'll explore how to write binary bytes directly to a file using Python's built-in `struct` module.

## Solution & Analysis

To solve the problem of writing binary bytes to a file, we need to use the `struct.pack()` function, which converts between Python values and C structs represented as Python `bytes` objects. In this case, we want to write a byte array directly to a file without any encoding or decoding.

```python
import struct

# Define the input data as a byte array
barray = bytearray()

# Define the input string
s = 'e0e1e2e3e4e5e6e7e8e9eaebecedeeef'

# Initialize counters and variables
i = 0
cnt = len(s)

while i < cnt:
    j = i + 1
    num = s[i] + s[j]
    num = int(num, 16)
    num = num ^ 239

    # Pack the binary data into a bytes object using struct.pack()
    xnum = struct.pack('B', num)

    barray += xnum
    i = j + 1

print('barray = \n' + str(barray))
```

In this corrected code, we use `struct.pack()` to convert the binary data into a bytes object. We then append this bytes object to our original byte array.

## Conclusion

By using the `struct.pack()` function, we can write binary bytes directly to a file without any encoding or decoding. This approach ensures that the output is as intended and avoids any potential issues with ASCII representations.