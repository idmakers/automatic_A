---
title: "Inherent Unchecked Integer Methods in Rust"
tags:
  - unchecked
  - integer
  - rust
---

# Inherent Unchecked Integer Methods in Rust

## Core Problem
The `unchecked_*` methods on integers have been available since Rust's stable version, but there are still unresolved questions and debates about their usage and implications.

## Solution & Analysis
### Steps / History

- [x] #69659 added `unchecked_{add,sub,mul}`
- [x] #85096
- [x] #85703
- [x] #103456
- [x] #115626 added `unchecked_neg`
- [x] #122520

### Unresolved Questions

- Should the other unchecked intrinsics like `exact_div` also get inherent versions?
- From #85703, consider if distinguishing the different kinds of ub could be useful (UB from overflow like `MIN/-1` or `MAX+1`, LLVM's `n[us]w`; UB from input range like `x/0` or `x << -1`; UB from lossy like `2/4` or `3 >> 1`, LLVM's `exact`)

### Resolved unresolved questions:

- <del>Is `unchecked_*` the best naming for these?</del> <ins>We stabilised `unchecked_{add,sub,mul}` already, so, yes.</ins>

### Final commenting period (FCP) and Stabilization PR
Once the feature has gone through a few release cycles and there are no unresolved questions left, the feature might be ready for stabilization.

## Conclusion
The inherent unchecked integer methods provide an additional layer of performance in certain cases. However, their usage should be carefully considered to avoid potential issues like overflowing or underflowing values.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/85122)