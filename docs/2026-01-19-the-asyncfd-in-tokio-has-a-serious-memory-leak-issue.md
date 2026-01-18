---
title: "Tokio's AsyncFd Memory Leak Issue"
tags:
  - tokio-rs
  - async-fd
  - memory-leak

---

# Tokio's AsyncFd Memory Leak Issue

## Core Problem
The `AsyncFd` in Tokio has a serious memory leak issue. When closing the file descriptor (fd) before dropping the `AsyncFd`, it breaks the contract mentioned in the docs and leads to resource leakage.

## Solution & Analysis
### Reproduction Code
```rust
use std::sync::Arc;
use std::time::Duration;
use tokio::io::unix::AsyncFd;

#[tokio::main]
async fn main() {
    loop {
        let fd = unsafe { libc::inotify_init1(libc::O_CLOEXEC | libc::O_NONBLOCK) };
        if fd == -1 {
            return;
        }
        let afd = Arc::new(fd);
        let async_fd = AsyncFd::new(afd.clone()).unwrap();
        unsafe {
            libc::close(fd);
        }
        tokio::time::sleep(Duration::from_millis(1)).await;
        drop(async_fd);
    }
}
```

### Analysis
The issue lies in the fact that `AsyncFd` takes ownership of an arbitrary object to represent the IO object. Closing the fd before dropping the `AsyncFd` breaks this contract.

The leakage is caused by because `driver::cleanup_intrusive_list` returns an error and interrupts the cleanup logic, leading to resource leakage in the intrusive list. To fix this issue, we should avoid cleaning up the intrusive list if `driver::cleanup_intrusive_list` fails.

However, unconditionally cleaning the intrusive list could break memory safety. Given that `driver::cleanup_intrusive_list` failure is a rare and extreme case, it becomes even more crucial to prioritize memory safety in such situations.

### Solution
To fix this issue, we can use `async_fd.into_inner()` before closing the fd. This ensures that the intrusive list is cleaned up safely.
```rust
use std::sync::Arc;
use std::time::Duration;
use tokio::io::unix::AsyncFd;

#[tokio::main]
async fn main() {
    loop {
        let fd = unsafe { libc::inotify_init1(libc::O_CLOEXEC | libc::O_NONBLOCK) };
        if fd == -1 {
            return;
        }
        let afd = Arc::new(fd);
        let async_fd = AsyncFd::new(afd.clone()).unwrap();
        tokio::time::sleep(Duration::from_millis(1)).await;
        drop(async_fd.into_inner());
        unsafe {
            libc::close(fd);
        }
    }
}
```

### Conclusion
By using `async_fd.into_inner()` before closing the fd, we can fix the memory leak issue in Tokio's `AsyncFd`. This ensures that the intrusive list is cleaned up safely and prevents resource leakage.

## Reference
- [Source](https://github.com/tokio-rs/tokio/issues/7563)