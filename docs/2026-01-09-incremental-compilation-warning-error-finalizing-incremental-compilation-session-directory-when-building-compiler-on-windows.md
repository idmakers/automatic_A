---
title: "Incremental Compilation Warning on Windows: A Step-by-Step Fix"
tags:
  - rustc
  - incremental compilation
  - windows
---

# Incremental Compilation Warning on Windows: A Step-by-Step Fix

## Core Problem
When building the Rust compiler on Windows with incremental compilation turned on, you may encounter a warning that prevents the compiler from continuing. The warning is:

`warning: Error finalizing incremental compilation session directory `\\?\C:\Users\ryanl\Code\rust\build\x86_64-pc-windows-msvc\stage0-rustc\x86_64-pc-windows-msvc\release\incremental\rustc_incremental-3eitboxu59x4j\s-g08srunmh1-1cw7crv-working`: Access is denied. (os error 5)`

## Solution & Analysis
The likely cause of this issue is a file handle that is still open. This can occur when the `fs::rename` operation fails, which can happen due to various reasons such as:

* A file or directory is being used by another process.
* The file system has issues with disk permissions.

To fix this issue, you can try the following steps:

### Step 1: Close any open files or directories

Make sure that no other process is using the same file handles. You can do this by closing any open files or directories in your IDE or terminal.

### Step 2: Run the compiler with elevated privileges

Try running the Rust compiler with elevated privileges using `runas` or `sudo`. This will give the compiler the necessary permissions to access and rename files on your system.

```bash
runas /user:administrator "rustc --release"
```

or

```bash
sudo rustc --release
```

### Step 3: Disable incremental compilation for now

If none of the above steps work, you can try disabling incremental compilation temporarily to see if the warning persists. You can do this by using the `--no-incremental` flag when compiling.

```bash
rustc --release --no-incremental
```

### Step 4: Check and fix file system permissions

If none of the above steps work, it's possible that there's an issue with your file system permissions. You can try checking and fixing any issues with disk permissions.

## Conclusion
By following these steps, you should be able to resolve the incremental compilation warning on Windows. If you're still experiencing issues, please check out the Rust language repository for more information and potential fixes.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/86929)