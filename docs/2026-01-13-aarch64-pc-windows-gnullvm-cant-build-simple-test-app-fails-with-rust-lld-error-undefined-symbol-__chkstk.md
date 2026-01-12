---
title: "Fixing `rust-lld` Error in `aarch64-pc-windows-gnullvm` Builds"
tags:
  - aarch64-pc-windows-gnullvm
  - rust-lld
  - undefined symbol __chkstk
---

# Fixing `rust-lld` Error in `aarch64-pc-windows-gnullvm` Builds

## Core Problem
The `aarch64-pc-windows-gnullvm` target is failing to build due to an error with the `rust-lld` linker. The specific issue is a reference to an undefined symbol: `__chkstk`.

## Solution & Analysis
To fix this issue, you need to install the `rust-mingw` component. This component provides the necessary libraries for building Rust applications on Windows.

### Install `rust-mingw`

Add the following line to your `.cargo/config.toml` file:
```toml
[target.aarch64-pc-windows-gnullvm]
linker = "rust-lld"
rust-link-libs = ["mingw32", "mingwex"]
```
The `rust-link-libs` option specifies the libraries that should be linked with the executable. In this case, we need to include the `mingw32` and `mingwex` libraries.

### Verify Installation

After installing `rust-mingw`, verify that it has been correctly installed by running:
```bash
cargo --version
```
This should display the version of Cargo and Rust that you are using.

## Conclusion
By adding the `rust-mingw` component to your `.cargo/config.toml` file, you can fix the error with the `aarch64-pc-windows-gnullvm` target. This will allow you to build your application successfully on Windows.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/150725)