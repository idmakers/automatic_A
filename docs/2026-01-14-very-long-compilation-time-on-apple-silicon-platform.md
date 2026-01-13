---
title: "Slow Compilation Times on Apple Silicon Platforms: A Rust Developer's Dilemma"
tags:
  - rust
  - apple-silicon
  - compilation-time
---

# Slow Compilation Times on Apple Silicon Platforms: A Rust Developer's Dilemma

## Core Problem
A common issue faced by many Rust developers is the slow compilation times on Apple Silicon platforms. This problem has been observed in various crates, including a specific example that reproduces the issue with 40+ minutes of compilation time on M1 and M3 devices, while compiling in under 2 minutes on Ryzen 7950X3D 96GB Linux.

## Solution & Analysis
The problem is attributed to the `finish_ongoing_codegen` function in the Rust compiler, which spends most of its time executing. According to the `-Ztime-passes` flag, this function accounts for a significant portion of the overall compilation time. A potential solution to this issue is made possible by changing the data type of certain fields from optional to non-optional.

```rust
// Original code
#[derive(Debug)]
struct Bug {
    // ...
}

// Modified code
#[derive(Debug)]
struct Bug {
    endpoint1: String,
    endpoint2: String,  // Changed from Option<String> to String
}
```

By making this change, the compilation time is significantly reduced.

## Conclusion
In conclusion, slow compilation times on Apple Silicon platforms can be a challenging issue for Rust developers. By understanding the source of the problem and applying targeted solutions, such as changing data types to non-optional fields, it is possible to improve compilation efficiency and reduce overall development time.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/129713)