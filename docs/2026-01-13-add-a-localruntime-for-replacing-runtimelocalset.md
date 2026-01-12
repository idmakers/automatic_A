---
title: "Simplifying Task Spawning in Tokio with LocalRuntime"
tags:
  - tokio
  - runtime
  - localset
---

# Simplifying Task Spawning in Tokio with LocalRuntime

## Core Problem
Currently, to spawn `!Send` tasks on Tokio, you must use a combination of a current-thread runtime and `LocalSet` to make that work. However, tasks on a `LocalSet` are separate from the rest of the runtime in an uncomfortable way.

## Solution & Analysis
```rust
use tokio::runtime::{Builder, CurrentThread};

// Introduce LocalRuntime type that behaves like a current-thread Runtime
pub struct LocalRuntime;

impl LocalRuntime {
    fn new() -> Self {
        // Create a new builder for a current-thread runtime
        let mut builder = Builder::new_multi_thread();
        
        // Set the local set to be the same as the current thread's local set
        builder.local_set();
        
        // Return the created runtime
        builder.build_current_thread().unwrap()
    }
}

impl Runtime for LocalRuntime {
    type Future = ();

    fn spawn<F: FnOnce() + Send>(&self, fut: F) -> Pin<Box<dyn Future<Self::Future> + 'static>> {
        Box::pin(self.current_thread().spawn(fut))
    }

    fn spawn_local<F: FnOnce() + Unpin>(&self, fut: F) -> Pin<Box<dyn Future<Self::Future> + Send + Unpin>> {
        // Since LocalRuntime is !Send, we can use tokio::spawn directly
        Box::pin(self.current_thread().spawn(fut))
    }
}
```

## Conclusion
The `LocalRuntime` type simplifies task spawning in Tokio by providing a more comfortable and efficient way to spawn `!Send` tasks. With this introduction, developers can easily spawn tasks on the local thread without relying on `LocalSet`.

## Reference
- [Source](https://github.com/tokio-rs/tokio/issues/6739)