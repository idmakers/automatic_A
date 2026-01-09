---
title: "Elevate Tokio Performance with LocalRuntime"
tags:
  - tokio-performance
  - runtime-localset
---

# Enhancing Tokio Performance with LocalRuntime

## Core Problem

Currently, to spawn `!Send` tasks on Tokio, developers must use a combination of a current-thread runtime and `LocalSet`. However, this approach has limitations, such as tasks on the `LocalSet` being separate from the rest of the runtime in an uncomfortable way. This can lead to performance issues and unexpected behavior.

## Solution & Analysis

### Introducing LocalRuntime

To address these concerns, we propose introducing a new type called `LocalRuntime`. This type behaves exactly like a current-thread `Runtime`, with the following key differences:

*   The `LocalRuntime` type is `!Send` to enable spawning `!Send` tasks directly onto the runtime.
*   From within a `LocalRuntime`, the `tokio::spawn` and `spawn_local` functions have identical behavior.

### Code Example

Here's an example of how you can use `LocalRuntime` in your Tokio code:

```rust
use tokio::{runtime::Builder, spawn, LocalSet};

// Create a new LocalRuntime with the default settings
let local_runtime = Builder::new_multi_thread().local_set(Default::default())
    .build()
    .expect("Failed to create LocalRuntime");

// Spawn a task on the LocalRuntime
spawn(async move {
    println!("Task executed successfully");
}).await?;

// Spawn a local task on the LocalRuntime
spawn_local(async move {
    println!("Local task executed successfully");
});

// Join the spawned tasks
local_runtime.spawn_blocking(|| {
    // Execute blocking code here
});
```

### Benefits and Analysis

The introduction of `LocalRuntime` provides several benefits:

*   **Improved performance**: By avoiding the use of `LocalSet`, we can reduce the performance overhead associated with spawning `!Send` tasks.
*   **Simplified task management**: With identical behavior for `tokio::spawn` and `spawn_local` within a `LocalRuntime`, developers can manage tasks in a more consistent and predictable way.

## Conclusion

The introduction of `LocalRuntime` addresses the limitations of the current approach to spawning `!Send` tasks on Tokio. By providing a new type that behaves like a current-thread `Runtime`, we can improve performance, simplify task management, and enhance overall developer experience.

## Reference
- [Source](https://github.com/tokio-rs/tokio/issues/6739)