---
title: "Wrong Architecture Objects Mixed in Self-Built Compiler on Apple Silicon Hosts"
tags:
  - rust-lang
  - compiler
  - apple-silicon
---

# Wrong Architecture Objects Mixed in Self-Built Compiler on Apple Silicon Hosts

## Core Problem

When using a self-built compiler on an Apple Silicon host to build a `x86_64-unknown-none` static library, `mach-o-arm64` objects get mixed into the output. This issue arises due to a recent change in Rust's compiler configuration.

## Solution & Analysis

To solve this problem, we need to update our `config.toml` file to include the following lines:
```toml
[llvm]
targets = "AArch64;X86"

[build]
target = [
    "aarch64-apple-darwin",
    "x86_64-unknown-none",
]

[rust]
llvm-tools = true
lld = true

[mach-o]
target-architecture = "arm64"
```
Additionally, we need to add the following line to our `foo.rs` file:
```rust
#![no_std]

#[panic_handler]
fn panic(_: &core::panic::PanicInfo) -> ! {
    loop {}
}

fn main() {}
```
We also need to update our build command to include the following flag:
```bash
$ rustc --crate-type staticlib --target x86_64-unknown-none --mach-o-arm64-unknown-none foo.rs
```
With these changes, we should be able to build a `x86_64-unknown-none` static library on an Apple Silicon host without any `mach-o-arm64` objects getting mixed in.

## Conclusion

By updating our `config.toml` file and adding the necessary lines to our code, we can solve the issue of wrong architecture objects being mixed in self-built compiler outputs on Apple Silicon hosts.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/105065)