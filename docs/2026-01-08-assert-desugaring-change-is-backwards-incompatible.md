---
title: "Backwards-Incompatible Assert Desugaring Change in Rust"
tags:
  - Rust
  - backwards-incompatibility
  - assert desugaring
---

# Backwards-Incompatible Assert Desugaring Change in Rust

## Core Problem

A recent change to Rust's assert desugaring has broken the `bitvec` crate, causing a compilation error on stable versions of the language. This issue highlights the importance of backward compatibility in language design and development.

## Solution & Analysis

```rust
#[derive(Debug)]
struct F {
    data: bool
}

impl std::ops::Not for F {
  type Output = bool;
  fn not(self) -> Self::Output { !self.data }
}

fn main() {
  let f = F { data: true };

  assert!(f);
}
```

The problem lies in the fact that the `assert!` macro now desugares to a call to `std::panic::catch_unwind`, which expects a closure with at least one argument. In the provided code, the `F` struct implements the `Not` trait, but its `not` method returns the value of `data`, not a boolean indicating whether the value is true or false.

To fix this issue, we need to modify the `assert!` macro to correctly handle the desugaring of closures that return values. One possible solution is to use the `std::ops::Not` trait's `not` method as intended:

```rust
fn main() {
  let f = F { data: true };

  assert!(f.not());
}
```

This change ensures that the `assert!` macro correctly desugares the closure and checks if the value of `data` is false.

## Conclusion

The backwards-incompatible assert desugaring change in Rust highlights the importance of careful consideration when designing language features. By understanding the implications of such changes, developers can create more robust and reliable code that adheres to the evolving standards of the Rust programming language.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/145770)