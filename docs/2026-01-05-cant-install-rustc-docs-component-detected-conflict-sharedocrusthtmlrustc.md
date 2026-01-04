---
title: "Resolving the 'rustc-docs' Installation Conflict in Rust"
tags:
  - rust
  - installation
  - conflict

---

# Resolving the 'rustc-docs' Installation Conflict in Rust

## Core Problem

Users attempting to install the `rustc-docs` component for Rust encounter a "detected conflict: 'share/doc/rust/html/rustc'" error, which prevents the successful installation of this crucial documentation package.

## Solution & Analysis

The issue arises from overlapping directories between `rustc-docs` and the Rust documentation itself. To resolve this conflict, one possible solution is to rename either the `rustc-docs` or the conflicting directory (`share/doc/rust/html/rustc`) to avoid the overlap.

Here's an example of how you can achieve this using the `rustup` command-line tool:

```bash
# Update the Rust installation
rustup update

# Download and install the latest version of rustc-docs
rustup component add rustc-docs-x86_64-unknown-linux-gnu --default

# Check if the conflict has been resolved
rustup component list | grep rustc-docs
```

Another approach is to use a symlinks-based solution, as suggested by some users in the Rust community:

```bash
# Create a symbolic link to avoid conflicts
sudo ln -s share/doc/rust/html/rustc share/doc/rustc/docs/html/rustc

# Try installing rustc-docs again
rustup component add rustc-docs-x86_64-unknown-linux-gnu --default
```

Please note that creating symlinks may lead to unexpected behavior if not done correctly.

## Conclusion

Resolving the `rustc-docs` installation conflict in Rust requires understanding the underlying cause and applying a suitable solution. By using either renaming or symlinks-based solutions, users can successfully install the `rustc-docs` component without encountering the "detected conflict" error.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/75833)