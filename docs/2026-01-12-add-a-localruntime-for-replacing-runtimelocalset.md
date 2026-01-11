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
### Introducing LocalRuntime

To address this issue, we propose introducing a new type called `LocalRuntime`, which behaves exactly like a current-thread `Runtime`. The key differences between `LocalRuntime` and `Runtime` are:

*   The `LocalRuntime` type is `!Send` to enable spawning `!Send` tasks directly onto the runtime.
*   From within a `LocalRuntime`, the `tokio::spawn` and `spawn_local` functions have identical behavior.
*   Tasks spawned using `spawn_local` are scheduled in the same way as normal `tokio::spawn` tasks.

### Implementation

The implementation of `LocalRuntime` will be similar to that of `Runtime`, with some modifications to accommodate its new features. Here is a high-level overview of how it could work:

```rust
use tokio::runtime::{Builder, Runtime};
use tokio::task::{JoinHandle, Spawner};

pub struct LocalRuntime {
    runtime: Runtime,
}

impl LocalRuntime {
    pub fn new() -> Self {
        Self {
            runtime: Builder::new_multi_thread().build().unwrap(),
        }
    }

    // Expose tokio::spawn and spawn_local with identical behavior
    pub async fn spawn<F, R>(&self, task: F) -> JoinHandle<R>
    where
        F: FnOnce() + Send + 'static,
    {
        self.runtime.spawn(task)
    }

    pub async fn spawn_local<F>(&self, task: F) -> JoinHandle<()>
    where
        F: FnOnce() + Send + 'static,
    {
        self.spawn(move || task())
    }
}
```

### Benefits

The introduction of `LocalRuntime` will provide several benefits:

*   Simplified task spawning: With `LocalRuntime`, you can spawn tasks using either `spawn` or `spawn_local`, without having to worry about the complexities of `LocalSet`.
*   Improved performance: By avoiding the overhead of `LocalSet`, tasks spawned on `LocalRuntime` should be faster.
*   Better compatibility with Deno and other runtimes.

### Conclusion

In this blog post, we introduced a new type called `LocalRuntime`, which simplifies task spawning in Tokio by providing a seamless experience for both `!Send` and non-`!Send` tasks. With its `!Send` behavior, identical `spawn` and `spawn_local` functionality, and performance benefits, `LocalRuntime` is an attractive alternative to the current approach using `Runtime` and `LocalSet`.

## Reference
- [Source](https://github.com/tokio-rs/tokio/issues/6739)