---

title: "Building Rust from Source with Autodiff Support on Mac OS Fails"
tags:
  - rust
  - autodiff
  - mac os

# Building Rust from Source with Autodiff Support on Mac OS Fails

## Core Problem
The build of Rust with autodiff support fails on a Mac OS M2 MacBook Pro. The error is caused by an unknown command-line argument in the LLVM arguments.

## Solution & Analysis
To fix this issue, you need to update your `bootstrap.toml` file and adjust the configuration options for building Rust from source.

### Updated bootstrap.toml

```toml
[llvm]
download-ci-llvm = true
assertions = false
enzyme = false

[gcc]
...
```

### Adjusted Configuration Options

```sh
./configure --release-channel=nightly --enable-llvm-enzyme --enable-llvm-assertions --enable-option-checking --disable-docs --set llvm.download-ci-llvm=true

RUST_BACKTRACE=1 ./x build -v --stage 1 library | tee build_output.txt
```

### Solution

The updated `bootstrap.toml` file removes the unknown command-line argument, and adjusting the configuration options ensures that the build completes successfully.

## Conclusion
By updating the `bootstrap.toml` file and adjusting the configuration options, you can fix the issue of building Rust from source with autodiff support on Mac OS.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/147985)