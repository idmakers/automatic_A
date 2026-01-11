A Bug in Rust's Thread-Local Storage: Erroneous Address Mapping on Unsupported Targets

### Introduction

When developing an operating system in Rust, it is essential to understand the intricacies of thread-local storage (TLS) and its behavior on different targets. However, a recent bug report has highlighted a peculiar issue with TLS on unsupported targets, which can lead to page faults and unexpected behavior.

### The Bug Report

A user reported an issue with using `#[thread_local]` on an x86-64 target without the `"os": "none"` attribute. The code snippet that triggered the bug is as follows:

```rust
#[thread_local]
static mut GLOBAL: i32 = 0;
```

The expected behavior was for the address of the static variable to remain valid, but instead, it went out of bounds and caused a page fault.

### Analysis

After analyzing the issue, it appears that `#[thread_local]` compiles to something that is not supported by the target's toolchain and loader. The bug report suggests that this may be due to the way TLS works on ELF targets with the `"os": "none"` attribute.

According to an overview of thread-local storage in ELF targets, the compiler generates special sections for TLS variables, which are not present on all targets. When the target does not support these sections, the compiler falls back to using a different approach, which may lead to unexpected behavior.

### Solution

To fix this issue, we need to ensure that the target's toolchain and loader support the necessary sections for TLS variables. One possible solution is to add the `"os": "none"` attribute to the target configuration, like so:

```json
{
    "llvm-target": "x86_64-unknown-none",
    "data-layout": "e-m:e-p270:32:32-p271:32:32-p272:64:64-i64:64-i128:128-f80:128-n8:16:32:64-S128",
    "arch": "x86_64",
    "target-endian": "little",
    "target-pointer-width": 64,
    "target-c-int-width": 32,
    "os": "none",
    "executables": true,
    "linker-flavor": "ld.lld",
    "linker": "rust-lld",
    "panic-strategy": "abort",
    "disable-redzone": true,
    "features": "-mmx,-sse,+soft-float",
    "rustc-abi": "x86-softfloat"
}
```

By adding this attribute, we ensure that the target's toolchain and loader support the necessary sections for TLS variables, which should fix the bug.

### Conclusion

In conclusion, this bug report highlights an important issue with thread-local storage in Rust on unsupported targets. By analyzing the problem and providing a solution, we can help developers who are building operating systems or other applications that rely on thread-local storage to ensure their code runs correctly on different targets.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/150573)