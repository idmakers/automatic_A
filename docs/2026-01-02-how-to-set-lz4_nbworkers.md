---
title: "How to Optimize LZ4 Compression with Multicore Support"
tags:
  - python
  - lz4
  - compression
  - multicore

---

# Core Problem

Despite having a multi-core CPU, the provided Python script does not demonstrate a significant speed improvement when setting `LZ4_NBWORKERS`. This is likely due to an incorrect approach or misunderstanding of how to properly utilize multithreading for LZ4 compression.

## Solution & Analysis

To take advantage of multicore CPUs with LZ4 compression, you need to use the correct flags and options. The `LZ4_NBWORKERS` option controls the number of threads used by the LZ4 library.

```python
import subprocess, time, platform, sys, os

# Information of the system
print('OS:', platform.platform())
print('CPU:', platform.processor())
print('Number of logical cores:', os.cpu_count())
print('Python:', platform.architecture()[0], platform.machine(), sys.version)

# Paths
lz4Path         = r"C:\Users\Akira\Downloads\Compressed\lz4_win64_v1_10_0\lz4.exe"
srcDir          = r"E:\Personal Projects\tmp"
filesToArchive  = ["chunk_0.ndjson", "chunk_0.ndjson"]
outLz4          = r"E:\Personal Projects\tmp\test.tar.lz4"

# Prepare the command
cmd     = f'"{lz4Path}" -C {srcDir} -f -c {len(filesToArchive)} | gzip -9 > {outLz4}'
start   = time.perf_counter()
result  = subprocess.run(cmd, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
end     = time.perf_counter()

print(f"The running time is {round(end-start, 2)} seconds.")
```

In this revised script, we use the `-C` flag to specify the directory where the input files are located. The `-f` flag indicates that gzip should be used to compress the output, and the `-c` flag specifies the compression level (in this case, -9 for best performance). Note that we also added the `gzip` command before redirecting the output to the `outLz4` file.

## Conclusion

By using the correct flags and options, you can take full advantage of your multicore CPU when compressing files with LZ4. Remember to consider factors like compression level and input/output directories to optimize performance.