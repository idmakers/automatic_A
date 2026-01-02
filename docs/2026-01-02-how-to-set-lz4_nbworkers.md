---
title: "How to Optimize LZ4 Compression with Multiple CPU Cores"
tags:
  - lz4
  - compression
  - multithreading
---

# Core Problem
LZ4 is a high-performance lossless compression library, but it can take advantage of multiple CPU cores for faster compression. However, the default behavior may not be optimized for multi-core CPUs, leading to suboptimal performance.

## Solution & Analysis
To optimize LZ4 compression with multiple CPU cores, you need to set the `LZ4_NBWORKERS` environment variable. This variable controls the number of worker threads used by LZ4 during compression.

Here's an example Python script that demonstrates how to set `LZ4_NBWORKERS` and measure its impact on compression performance:
```python
import subprocess, time, platform, sys, os

# Information of the system
print('OS:', platform.platform())
print('CPU:', platform.processor())
print('Number of logical cores:', os.cpu_count())
print('Python:', platform.architecture()[0], platform.machine(), sys.version)

# Paths
tarPath         = "tar"  # Windows 11 has tar.exe on PATH
lz4Path         = r"C:\Users\Akira\Downloads\Compressed\lz4_win64_v1_10_0\lz4.exe"
srcDir          = r"E:\Personal Projects\tmp"
filesToArchive  = ["chunk_0.ndjson", "chunk_1.ndjson"]
outLz4          = r"E:\Personal Projects\tmp\test.tar.lz4"

# Version of lz4
print()
result  = subprocess.run([lz4Path, "-V"], capture_output=True, text=True, check=True)
print("STDOUT:", result.stdout.strip())
print()

# Prepare the command
for nCore in range(1, 10):
    cmd     = f'set LZ4_NBWORKERS={nCore} & && tar -C "{srcDir}" -cf - {"".join(filesToArchive)} | {"lz4Path"} -f - "{outLz4}'
    start   = time.perf_counter()
    result  = subprocess.run(cmd, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    end     = time.perf_counter()
    print(f"The running time with {nCore} threads is", round(end-start), "seconds.")
```
In this script, we iterate through different values of `LZ4_NBWORKERS` (from 1 to 9) and measure the compression time for each value. We use the `set` command to set the environment variable `LZ4_NBWORKERS` before running the tar command with LZ4 compression.

## Conclusion
By setting the correct value for `LZ4_NBWORKERS`, you can optimize the performance of LZ4 compression on multi-core CPUs. The optimal value may vary depending on the specific use case and system configuration. Experimenting with different values can help you find the sweet spot for your application.