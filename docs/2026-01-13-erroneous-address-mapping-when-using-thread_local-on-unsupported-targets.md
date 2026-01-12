**Erroneous Address Mapping with `#[thread_local]` on Unsupported Targets**

### Core Problem

When using the `#[thread_local]` attribute in Rust to create a global static variable, it can lead to erroneous address mapping on unsupported targets. This issue is particularly problematic when developing an operating system (OS) in Rust.

### Solution & Analysis

The problem lies in how the `#[thread_local]` attribute interacts with the target's ELF layout and relocations. On normal ELF targets, thread-local storage is managed by the linker, which generates special sections for thread-local variables. However, when targeting an unsupported architecture like `"os": "none"`, the compiler generates code that relies on specific relocation patterns to access these sectioned addresses.

The issue arises because `#[thread_local]` generates code that attempts to map the static variable's address at a specific location in memory, which is not supported by the target. This results in an out-of-bounds error and eventually leads to a page fault.

To demonstrate this issue, consider the following example:
```rust
#[thread_local]
static mut GLOBAL: i32 = 0;

fn main() {
    println!("{}", unsafe { &*GLOBAL });
}
```
On a normal ELF target like `"llvm-target": "x86_64-unknown-linux-gnu"`, this code will print `0`. However, when targeting `"os": "none"` with the same code, it will produce an out-of-bounds error and crash.

To fix this issue, you can use the following workaround:
```rust
#[thread_local]
static GLOBAL: i32 = 0;

fn main() {
    println!("{}", GLOBAL);
}
```
By removing the `mut` keyword and using a non-static variable, we bypass the need for thread-local storage and avoid the out-of-bounds error.

### Conclusion

When developing an OS in Rust or targeting unsupported architectures with `#[thread_local]`, it is essential to be aware of these potential issues. By understanding how the attribute interacts with the target's ELF layout and relocations, you can take steps to mitigate these errors and ensure your code runs reliably on a wide range of targets.

**Example Use Cases**

*   Developing an OS in Rust
*   Targeting unsupported architectures like `"os": "none"`
*   Using `#[thread_local]` with static variables

**Related Issues**

*   [Thread-local storage issues](https://maskray.me/blog/2021-02-14-all-about-thread-local-storage)
*   [Rust's ELF target support](https://github.com/rust-lang/rust/issues/77415)

## Reference
- [Source](https://github.com/rust-lang/rust/issues/150573)