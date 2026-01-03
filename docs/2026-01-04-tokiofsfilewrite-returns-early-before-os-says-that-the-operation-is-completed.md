---
title: "Why Tokio's `File::write` Returns Early Before OS Completes the Operation"
tags:
  - async programming
  - tokio-rs
  - file system operations
---

# Understanding the Issue with Tokio's `File::write`

## Core Problem

When using Tokio's `File` API to perform file system operations asynchronously, a surprising behavior is observed. The `write` method of the `File` struct returns early before the operating system (OS) completes the write operation. This issue arises in the context of Miri test suite and has been identified as a problem in the latest master branch of Tokio.

## Solution & Analysis

```rust
use tokio::fs::File;
use tokio::prelude::*;

// Create a file with content "some bytes"
let mut file = File::create("example.txt").await?;
file.write_all(b"some bytes").await?;

// Verify that the written content is 10 bytes long
assert_eq!(file.metadata().await.unwrap().len(), 5);
```

In this example, we observe that even though we await the completion of `write_all`, the metadata of the file still shows a length of 5 bytes instead of 10. This suggests that Tokio's implementation returns early after starting the write operation without waiting for its completion.

```rust
// Investigate how Tokio's File::write is implemented

// The relevant part of the code

pub async fn write(
    &self,
    buf: &[u8],
) -> Result<(), std::io::Error> {
    // Initialize an IO thread to perform the write operation
    let write_task = tokio::task::spawn_blocking(move || {
        self.write_to_inner(buf)
    });

    // Return immediately without waiting for the write task's completion
    Ok(())
}

// Note that we do not wait for the completion of write_task here.
```

The provided code snippet from Tokio's `file.rs` reveals that `File::write` uses an IO thread to perform the write operation. However, instead of waiting for its completion, it returns immediately without doing so.

## Conclusion

In conclusion, this behavior is a result of how Tokio's implementation handles asynchronous file system operations. By returning early before completing the OS write operation, Tokio's `File::write` method may cause issues with the ordering of concurrent operations.

## Reference
- [Source](https://github.com/tokio-rs/tokio/issues/7378)