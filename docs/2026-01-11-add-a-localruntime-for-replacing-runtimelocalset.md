---
title: "Replacing Runtime+LocalSet with a LocalRuntime in Tokio"
tags:
  - async programming
  - tokio-rs
---

# Simplifying Task Scheduling in Tokio with LocalRuntime

## Core Problem
Currently, spawning `!Send` tasks on Tokio requires combining a current-thread runtime and `LocalSet`, leading to performance overhead and unexpected behavior.

## Solution & Analysis
### Introducing LocalRuntime

To address the issue, we propose introducing a new type called `LocalRuntime`. This type behaves like a current-thread `Runtime`, but with additional features:

```rust
struct LocalRuntime {
    // ...
}

impl Runtime for LocalRuntime {
    fn new() -> Self {
        // ...
    }

    // ...
}

impl LocalSet for LocalRuntime {
    // ...
}
```

### Benefits of LocalRuntime

The proposed `LocalRuntime` has several benefits:

*   It is `!Send`, allowing direct spawning of `!Send` tasks onto the runtime.
*   Within a `LocalRuntime`, `tokio::spawn` and `spawn_local` have identical behavior, making it easier to manage tasks.
*   Tasks spawned using `spawn_local` are scheduled in the same way as normal `tokio::spawn` tasks.

```rust
struct LocalSet {
    runtime: LocalRuntime,
}

impl LocalSet for LocalRuntime {
    fn new() -> Self {
        LocalRuntime { /* ... */ }
    }

    // ...
}
```

### Comparison with Current Implementation

The proposed `LocalRuntime` provides a more efficient and intuitive way to manage tasks in Tokio. By introducing this new type, we can simplify the task scheduling process and reduce performance overhead.

## Conclusion
Replacing the current runtime+local set implementation with `LocalRuntime` offers several benefits, including improved efficiency, reduced complexity, and enhanced developer experience. This proposal aims to enhance the overall Tokio ecosystem and provide a better foundation for building high-performance asynchronous applications.

## Reference
- [Source](https://github.com/tokio-rs/tokio/issues/6739)