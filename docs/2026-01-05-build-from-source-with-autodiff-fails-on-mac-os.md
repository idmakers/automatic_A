---
title: "Building Rust with Autodiff on Mac OS Fails"
tags:
  - rust
  - autodiff
  - mac os
---

# Building Rust with Autodiff on Mac OS Fails

## Core Problem
When trying to build Rust with autodiff support from source, the process fails on a Mac OS M2 MacBook Pro. The exact error message is not provided, but it's known that the suggested configuration has been updated recently.

## Solution & Analysis

To troubleshoot this issue, we'll analyze the provided command and bootstrap configuration. The command used to build Rust with autodiff support includes:

```sh
git clone git@github.com:rust-lang/rust
cd rust
./configure --release-channel=nightly --enable-llvm-enzyme --enable-llvm-assertions --enable-option-checking --disable-docs --set llvm.download-ci-llvm=true

RUST_BACKTRACE=1 ./x build -v --stage 1 library | tee build_output.txt
```

The bootstrap configuration file (`bootstrap.toml`) is also provided. The relevant section for autodiff support is:

```toml
[llvm]
download-ci-llvm = true
assertions = true
enzyme = true
```

To solve this issue, we need to try different configurations and verify if the problem persists.

### Try Different Configurations

Let's try setting `llvm.enzyme` to `false` to isolate whether it's a problem with autodiff or Enzyme:

```sh
./configure --enable-llvm-enables --release-channel=nightly --enable-llvm-assertions --enable-option-checking --disable-docs --set llvm.download-ci-llvm=true
```

If the build still fails, we can try other configurations.

### Additional Troubleshooting Steps

To gather more information, we can enable verbose mode and backtrace:

```sh
RUST_BACKTRACE=1 RUST_LOG=debug ./x build -v --stage 1 library | tee build_output.txt
```

This will provide a detailed output of the build process.

## Conclusion
By analyzing the command and bootstrap configuration, we've identified potential issues with autodiff support. Trying different configurations and enabling verbose mode can help us gather more information about the problem.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/147985)