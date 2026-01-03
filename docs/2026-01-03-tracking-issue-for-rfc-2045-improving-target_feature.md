---
title: "Simplifying Target Features in Rust"
tags:
  - rust
  - rfc2045
  - feature gates

---

# Simplifying Target Features in Rust

## Core Problem
The current implementation of `#[target_feature]` in Rust can be complex and difficult to understand, leading to inconsistencies and bugs. The purpose of this blog post is to track the progress of stabilizing `#[target_feature]` as per RFC 2045.

## Solution & Analysis
### Implementing Proposed Semantics

To simplify the implementation of `#[target_feature]`, we need to stabilize the following feature gates:

* aarch64_unstable_target_feature
* aarch64_ver_target_feature
* arm_target_feature
* bpf_target_feature
* csky_target_feature
* ermsb_target_feature
* hexagon_target_feature
* lahfsahf_target_feature
* loongarch_target_feature
* mips_target_feature
* powerpc_target_feature
* prfchw_target_feature
* riscv_target_feature
* rtm_target_feature
* s390x_target_feature
* sse4a_target_feature
* tbm_target_feature
* wasm_target_feature
* x87_target_feature

Here is an example of how the stabilized `#[target_feature]` could look like:

```rust
#[cfg(target_feature = "sse4.2")]
fn function() {
    // code using sse4.2 instructions
}

// The basic set (sse–sse4.2, avx, avx2, ...) is implemented as follows:
#[cfg(target_feature = "_mm256_f32_rup")]
fn function() {
    // code using AVX-256 instructions
}
```

### Documenting Semantics

The stabilized `#[target_feature]` semantics are documented here: https://github.com/rust-lang/reference/pull/545.

### API Breaking Changes

To ensure a smooth transition to the new `#[target_feature]`, we need to make some API breaking changes:

* Allow `#[target_feature]` on unsafe functions only
* Change `#[target_feature = "+feature"]` to `#[target_feature(enable = "feature")]`

Here is an example of how the updated code could look like:

```rust
// Before:
#[cfg(target_feature = "sse4.2")]
fn function() {
    // code using sse4.2 instructions
}

// After:
#[cfg(target_feature(enable = "sse4.2"))]
fn function() {
    // code using sse4.2 instructions
}
```

### Related Tasks

* Fix bug: https://github.com/rust-lang/rust/issues/42515
* Resolve bug: https://github.com/rust-lang/rust/issues/44367
* Resolve issue: https://github.com/rust-lang/rust/issues/142412

### Consensus on API for Run-time Feature Detection

It is essential to have a consensus on the API for run-time feature detection. The current implementation can be improved, and we need to discuss this further.

## Conclusion
The stabilized `#[target_feature]` will simplify the implementation of target features in Rust and ensure a smooth transition to the new semantics. With the proposed API breaking changes and related tasks, we can ensure a stable and consistent implementation of `#[target_feature]`.