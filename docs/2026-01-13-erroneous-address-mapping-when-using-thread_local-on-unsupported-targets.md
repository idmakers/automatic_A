# Erroneous Address Mapping When Using `#[thread_local]` on Unsupported Targets

## Core Problem

When using the `#[thread_local]` attribute in Rust on unsupported targets, it can lead to erroneous address mapping, causing page faults and other runtime errors.

## Solution & Analysis

The issue arises when the compiler generates code for the `#[thread_local]` attribute that is not compatible with the target's memory layout. In this case, the target is `"os": "none"`, which means it's an unsupported operating system.

To understand why this happens, let's dive into how `#[thread_local]` works on normal ELF targets. As explained in [this article](https://maskray.me/blog/2021-02-14-all-about-thread-local-storage), `#[thread_local]` creates a new thread-local storage entry for each thread. This involves creating a new section in the ELF file and setting up the necessary relocations.

However, on an unsupported target like `"os": "none"`, the compiler may not generate code that is compatible with the target's memory layout. For example, the target may have different endianness or pointer width, which can cause issues when trying to access the thread-local storage.

To fix this issue, you can try one of the following solutions:

### Solution 1: Use a supported target

Switch to a supported target by modifying your `rustc` configuration. For example, you can change `"os": "none"` to `"os": "linux-gnu"` or `"os": "windows"`.

```json
{
    "llvm-target": "x86_64-unknown-linux-gnu",
    "data-layout": "e-m:e-p270:32:32-p271:32:32-p272:64:64-i64:64-i128:128-f80:128-n8:16:32:64-S128",
    ...
}
```

### Solution 2: Use a custom linker script

If you need to use an unsupported target, you can try using a custom linker script to handle the thread-local storage. This involves creating a linker script that sets up the necessary relocations and memory mapping for the `#[thread_local]` attribute.

For example, you can add the following code to your `linker script` file:
```rust
; Define the global section for the thread-local storage
global = _GLOBAL_OFFSET_SIZE;
type = * @GLOBAL_SIZE;
section = .LThreadLocal;

; Set up the relocations for the thread-local storage
relocation section .LThreadLocal {
    type == GLIBC_X86_64_TLS_DPLRDT32;
    #define TLS_DPLRDT32 0x10
}

; Map the thread-local storage to memory
memory region .LThreadLocal {
    base = @GLOBAL_SIZE;
    size = @GLOBAL_SIZE;
}
```

This custom linker script sets up a global section for the thread-local storage and defines the necessary relocations and memory mapping. Note that this is just an example, and you may need to modify it to fit your specific use case.

### Solution 3: Use `#[thread_local]` with caution

If you're working on an unsupported target, it's generally recommended to exercise caution when using `#[thread_local]`. This attribute can be useful for certain applications, but it's not always compatible with all targets.

In this case, the issue is caused by the compiler generating code that is not compatible with the target's memory layout. By switching to a supported target or using a custom linker script, you can work around this issue and use `#[thread_local]` safely.

## Conclusion

When working on an unsupported target, it's essential to be aware of the potential issues with `#[thread_local]`. By understanding how this attribute works on normal ELF targets and using one of the solutions outlined above, you can minimize the risk of erroneous address mapping and ensure that your code runs smoothly.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/150573)