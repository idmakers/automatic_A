---
title: "A stacktrace in RISC-V builds with `force-frame-pointers=yes` and `opt-level=z`: The Unexplained Disappearance"
tags:
  - riscv32imc-unknown-none-elf
  - force-frame-pointers=yes
  - opt-level=z
---

# Core Problem

When building a Rust program for the RISC-V CPU (`riscv32imc-unknown-none-elf`) with `force-frame-pointers=yes` and running it in release mode with `opt-level = "z"`, the stacktrace disappears as soon as execution enters the panicking code, resulting in only two frames being reported in GDB.

## Solution & Analysis

To reproduce this issue, create a new Rust project using Cargo:

```toml
[package]
name = "riscv_force_frame_pointers"
version = "0.1.0"

[dependencies]
panic_abort = "0.2.3"
riscv_rt = "0.20.0"
some_hal_crate = { path = "path/to/some/hal/crate" }
```

Add the following configuration to `.cargo/config.toml`:

```toml
[build]
rustflags = [
    # ...
    "-C", "force-frame-pointers=yes",
]
# ...

[unstable]
build-std = ["core", "panic_abort"]
```

Create a simple program that panics and uses the `panic_handler` macro to print a message:

```rs
#![no_std]
#![no_main]

use some_hal_crate::uart::Uart;
use riscv_rt::entry;

const UART_ADDR: *const () = (0b11 << 30) as *const ();

#[panic_handler]
fn panic_handler(info: &core::panic::PanicInfo) -> ! {
    let mut uart = Uart::new(UART_ADDR);
    writeln!(uart, "{info:?}").unwrap();
}

#[entry]
fn main() -> ! {
    skooks();
    loop {}
}
```

Build the program with `opt-level=z` and run it in release mode:

```bash
cargo build --release -C opt-level=z
target/debug/riscv_force_frame_pointers
```

Note that when running this program, GDB will not display a stacktrace beyond two frames. The exact location of the crash is not reported.

However, when building with `opt-level=z` but in debug mode (`debug = true`, `split-debuginfo = "unpacked"`), the issue does not occur:

```bash
cargo build --release -C opt-level=z -C debug=true
target/debug/riscv_force_frame_pointers
```

In this case, GDB will display a complete stacktrace when the program panics.

## Conclusion

This problem appears to be specific to release builds with `opt-level = "z"`. The exact cause of this issue is not yet understood.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/136198)