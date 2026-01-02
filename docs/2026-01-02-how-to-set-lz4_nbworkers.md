---
title: Optimizing LZ4 Compression with Multiple CPU Cores
tags: Python, LZ4, compression, multi-core CPUs, performance optimization
---

## Core Problem

LZ4 is a popular compression library that can take advantage of multiple CPU cores to improve compression speed. However, setting the `LZ4_NBWORKERS` environment variable correctly is crucial for achieving optimal performance on multi-core systems.

## Solution & Analysis

To set `LZ4_NBWORKERS`, you need to understand how it works and what values are supported. The `NBWORKERS` parameter controls the number of worker threads used by LZ4 to compress data in parallel. A higher value can lead to better compression performance, but also increases memory usage.

```python
import subprocess, time, platform, sys, os

# Set LZ4_NBWORKERS environment variable
for nCore in range(1, 16):  # Adjust the range based on your system's logical cores
    cmd = f"set LZ4_NBWORKERS={nCore} & && tar -C '{srcDir}' -cf - {' '.join(filesToArchive)} | {lz4Path} -f - {outLz4}"
    start = time.perf_counter()
    result = subprocess.run(cmd, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    end = time.perf_counter()
    print(f"The running time with {nCore} threads is {round(end-start)} seconds.")
```

In the example code above, we're using a `for` loop to iterate over different values of `NBWORKERS`. We set the environment variable using `set LZ4_NBWORKERS={nCore}`, and then run the tar command with the compression pipe.

Note that you should adjust the range in the `for` loop based on your system's logical cores. Also, be cautious when setting a high value for `NBWORKERS`, as it can lead to increased memory usage.

## Conclusion

Setting `LZ4_NBWORKERS` correctly is crucial for achieving optimal compression performance on multi-core systems. By understanding how `NBWORKERS` works and experimenting with different values, you can find the sweet spot that balances compression speed and memory usage.