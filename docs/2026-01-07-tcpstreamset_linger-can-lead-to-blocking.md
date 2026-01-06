---
title: "TcpStream::set_linger Can Lead to Blocking in Tokio"
tags:
  - tokio
  - TcpStream
  - Linux
  - SO_LINGER
---

# TcpStream::set_linger Can Lead to Blocking in Tokio

## Core Problem
The `tokio::net::TcpStream` has a `set_linger` function, which sets the `SO_LINGER` option on the underlying socket. This can cause the `close()` and `shutdown()` syscalls to block on Linux until all pending data is sent for a specified period of time.

## Solution & Analysis
```rust
use std::{io, time::Duration};

use tokio::{io::AsyncWriteExt, net::TcpStream};
use tracing::info;

#[tokio::main]
async fn main() -> io::Result<()> {
    // Initialize the tracer
    tracing_subscriber::fmt::init();

    let mut stream = TcpStream::connect("45.79.112.203:4242").await?;
    
    // Commenting out set_linger can improve performance
    // stream.set_linger(Some(Duration::from_secs(10))).await?;
    
    info!("Socket connected");
    stream.write_all(&vec![0u8; 1024 * 1024]).await?;
    info!("Data sent");
    drop(stream);
    info!("Socket closed");

    Ok(())
}
```

```rust
// To avoid blocking, we can set linger to None or 0.
stream.set_linger(None).await?;
// stream.set_linger(Some(Duration::from_secs(0))).await?;

// Alternatively, we can use the `linger` option when connecting the stream.
let mut stream = TcpStream::connect("45.79.112.203:4242", std::net::SocketAddr::new("127.0.0.1", 4242)).await?;
stream.set_linger(None).await?;

info!("Socket connected");
stream.write_all(&vec![0u8; 1024 * 1024]).await?;
info!("Data sent");
drop(stream);
info!("Socket closed");
```

## Conclusion
Setting `linger` to `None` or `0` can improve the performance of `TcpStream` by avoiding blocking on `close()` and `shutdown()` syscalls. It's recommended to use `linger` option when connecting the stream instead of setting it after connection.

<!-- ADSENSE_PLACEHOLDER -->

## Reference
- [Source](https://github.com/tokio-rs/tokio/issues/7751)