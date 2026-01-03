---
title: "Tracking Issue for RFC 2045: Improving `#[target_feature]`"
tags:
  - Rust Feature Gates
  - RFC 2045
---

# Tracking Issue for RFC 2045: Improving `#[target_feature]`

## Core Problem
The `#[target_feature]` attribute, introduced in RFC 2045, provides a way to conditionally compile code based on the target architecture's feature set. However, its usage and semantics are not yet fully stabilized.

## Solution & Analysis

### Implementing Proposed Semantics

To implement the proposed `#[target_feature]` semantics, we need to add support for the following feature gates:

```rust
// Enable or disable features for a specific target
#[cfg(target_feature = "aarch64_unstable_target_feature")]
fn foo() {
    // Code for aarch64_unstable_target_feature only
}

// Allow `#[target_feature]` on unsafe functions only
#[unsafe_fn]
#[cfg(target_feature = "+feature")]
fn bar() {
    // Code for the specified feature gate
}
```

### Documenting Semantics

The proposed semantics are documented in RFC 2045 and can be found at [https://github.com/rust-lang/reference/pull/545](https://github.com/rust-lang/reference/pull/545).

### Stabilization Plan

To stabilize `#[target_feature]`, we need to:

1. Implement the basic set of features for x86_64 and i686:
```rust
// Enable or disable features for a specific target (basic set)
#[cfg(target_arch = "x86_64")]
fn baz() {
    // Code for x86_64
}

#[cfg(target_arch = "i686")]
fn qux() {
    // Code for i686
}
```

2. Add support for ARM, AArch64, Hexagon, PowerPC, and MIPS:
```rust
// Enable or disable features for a specific target (arm)
#[cfg(target_feature = "arm_target_feature")]
fn foo() {
    // Code for arm
}

// Enable or disable features for a specific target (aarch64)
#[cfg(target_feature = "aarch64_ver_target_feature")]
fn bar() {
    // Code for aarch64
}
```

### API Breaking Changes

To improve the stability of `#[target_feature]`, we need to make some API breaking changes:

1. Allow `#[target_feature]` on unsafe functions only:
```rust
// Allow `#[target_feature]` on unsafe functions only
#[unsafe_fn]
#[cfg(target_feature = "+feature")]
fn baz() {
    // Code for the specified feature gate
}
```

2. Change `#[target_feature = "+feature"]` to `#[target_feature(enable = "feature")]`:
```rust
// Enable or disable features for a specific target (new syntax)
#[cfg(target_feature(enable = "feature"))]
fn qux() {
    // Code for the specified feature gate
}
```

### Related Tasks

To further improve `#[target_feature]`, we need to:

1. Fix bug: https://github.com/rust-lang/rust/issues/42515
2. Resolve bug: https://github.com/rust-lang/rust/issues/44367
3. Implement runtime feature detection:
```rust
// Runtime feature detection using the `cfg` macro
#[cfg(feature = "feature")]
fn foo() {
    // Code for the specified feature gate
}
```

### Conclusion

The `#[target_feature]` attribute is an essential tool for conditional compilation in Rust. By implementing the proposed semantics, documenting its usage, and making API breaking changes, we can improve its stability and usability.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/44839)