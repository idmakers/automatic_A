---
title: "How to Write Binary Bytes to File in Python"
tags:
  - binaryfile
  - python
  - fileio
---

# Writing Binary Bytes to File in Python

## Core Problem
When working with binary data, such as image files, it's essential to write the raw binary bytes to a file instead of ASCII representations. This can lead to issues when trying to read or process the data.

## Solution & Analysis
To write binary bytes to a file in Python, you can use the `open` function with the `'wb'` mode, which stands for "write binary." Here's an example:

```python
import struct

barray = bytearray()
s = 'e0e1e2e3e4e5e6e7e8e9eaebecedeeef'
i = 0
cnt = len(s)
while i < cnt:
    j = i + 1
    num = s[i] + s[j]
    num = int(num, 16)
    num = num ^ 239
    xnum = hex(num)[2:]
    if num < 16: #if > 16, then add the leading 0 to the hex value to ensure 2-digits
        xnum = xnum[:2] + '0' + xnum[2:]
    bnum = bytes(xnum, 'utf-8')
    barray += bnum
    i = j + 1

print('barray = \n' + str(barray))

outpath = 'C:\\Users\\jeffe\\Programming\\Python\\'
filename = 'hexformattest.jpeg'
savefilename = os.path.join(outpath + '\\' + filename)

with open(savefilename, 'wb') as binaryfile:
    binaryfile.write(barray)
```

In this code, we use the `with` statement to ensure that the file is properly closed after writing. We also use the `barray` variable directly when calling `write`, which will write the raw binary bytes to the file.

## Conclusion
By using the `'wb'` mode with the `open` function and writing the raw binary bytes, you can ensure that your binary data is written correctly to a file in Python.