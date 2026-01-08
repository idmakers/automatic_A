---
title: "Introducing LocalRuntime: Simplifying Task Spawning in Tokio"
tags:
  - tokio-rs
  - runtime
  - localset
---

# Introducing LocalRuntime: Simplifying Task Spawning in Tokio

## Core Problem
Currently, spawning `!Send` tasks on Tokio requires a combination of a current-thread runtime and `LocalSet`, leading to performance overhead and confusing behavior. This proposal introduces a new type called `LocalRuntime`, which simplifies task spawning while maintaining compatibility with the existing runtime.

## Solution & Analysis

To address the issues with `LocalSet`, we introduce a new type `LocalRuntime` that behaves like a current-thread `Runtime`. The key differences are:

*   `LocalRuntime` is `!Send`, enabling direct spawning of `!Send` tasks onto the runtime.
*   Within a `LocalRuntime`, `tokio::spawn` and `spawn_local` have identical behavior, allowing for seamless task switching between normal and local tasks.
*   Tasks spawned using `spawn_local` are scheduled similarly to normal `tokio::spawn` tasks.

Here's an example implementation of the `LocalRuntime` type:

```rust
use tokio::runtime::{Builder, CurrentSchedExt};
use tokio::sync::oneshot;
use futures::channel::{oneshot as oneshot_channel,mpsc};

struct LocalRuntime {
    // runtime fields...
}

impl LocalRuntime {
    fn new() -> Self {
        // create a new local runtime
        todo!()
    }

    async fn spawn<F>(&self, f: F)
    where
        F: FnOnce() + Send + 'static,
    {
        // spawn the task on the local runtime
        todo!()
    }

    async fn spawn_local<F>(&self, f: F)
    where
        F: FnOnce() + Send + 'static,
    {
        // spawn the task locally
        todo!()
    }
}
```

To use `LocalRuntime`, you can create a new instance and use its methods to spawn tasks:

```rust
#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let local_runtime = LocalRuntime::new().await?;

    // Spawn a task on the local runtime
    local_runtime.spawn(async { println!("Task spawned locally") }).await?;

    // Spawn a task using spawn_local
    local_runtime.spawn_local(async { println!("Task spawned normally") }).await?;

    Ok(())
}
```

## Conclusion

The introduction of `LocalRuntime` simplifies task spawning in Tokio by providing a new, more efficient way to handle tasks that require direct access to the runtime. This change improves performance and reduces confusion around task behavior, making it easier for developers to write high-performance concurrent code with Tokio.

## Reference
- [Source](https://github.com/tokio-rs/tokio/issues/6739)