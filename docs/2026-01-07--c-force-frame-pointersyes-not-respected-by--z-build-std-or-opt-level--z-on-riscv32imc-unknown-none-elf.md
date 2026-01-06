---
title: "A bug in RISC-V compilation with -C force-frame-pointers=yes and opt-level = 'z'"
tags:
  - riscv32imc-unknown-none-elf
  - debug builds
  - release builds
  - frame-pointers

---

# Frame Pointers Not Saved on Release Builds

## Core Problem

When compiling Rust code with the `-C force-frame-pointers=yes` flag and running it in a release build configuration (`opt-level = "z"`), the stack trace information is not saved, resulting in an incomplete backtrace when a panic occurs. This issue affects RISC-V (`riscv32imc-unknown-none-elf`) targets.

## Solution & Analysis

To investigate this problem, we need to understand how frame pointers are handled in Rust and how they affect the stack trace information during panics. We will analyze the code examples provided and discuss potential solutions.

### Code Example for Debug Builds

```rust
#![no_std]
#![no_main]

// ...

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

In debug builds with the `-C force-frame-pointers=yes` flag, the frame pointer is saved, and a complete stack trace is available when a panic occurs.

### Code Example for Release Builds

```rust
#![no_std]
#![no_main]

// ...

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

However, in release builds with `opt-level = "z"` and the same `-C force-frame-pointers=yes` flag, the frame pointer is not saved during panics, resulting in an incomplete stack trace.

## Conclusion

The issue seems to be related to the optimization level used for release builds (`opt-level = "z"`). It's recommended to use a lower optimization level, such as `opt-level = "s"`, to ensure that frame pointers are saved and complete stack traces are available during panics.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/136198)