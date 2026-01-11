---
title: "Building Issues with Rust's Nightly Toolchain"
tags:
  - rust-nightly-toolchain
  - building-issues
  - linux

---

# Building Issues with Rust's Nightly Toolchain

## Core Problem

When attempting to build the hypermine repository using Rust's nightly toolchain on Linux, the build process hangs at the last few steps. This issue is reproducible both locally on Linux Mint and in GitHub's CI environment (Ubuntu). In contrast, the stable toolchain does not exhibit this behavior.

## Solution & Analysis

To reproduce the issue:

1. Switch to the nightly toolchain:
```bash
$ rustup default nightly
```
2. Fetch the hypermine repository and clone it:
```bash
$ git clone https://github.com/danieldeankon/hypermine.git
$ cd hypermine/
```
3. Pull the latest changes from Git:
```bash
$ git lfs pull
```
4. Attempt to build the binaries with Cargo in verbose mode:
```bash
$ cargo build --release -v
```

The build process appears to hang after compiling the client module, as indicated by this output snippet:

```
   Compiling client v0.1.0 (/home/daniel/git/testinghypermine/hypermine/client)
     Running `rustc --crate-name client --edition=2018 client/src/lib.rs --error-format=json --json=diagnostic-rendered-ansi --crate-type lib --emit=dep-info,metadata,link -C opt-level=3 -C embed-bitcode=no --cfg 'feature="default"' --cfg 'feature="use-repo-assets"' -C metadata=c38d85b37e3e2e40 -C extra-filename=-c38d85b37e3e2e40 --out-dir /home/daniel/git/testinghypermine/hypermine/target/release/deps -L dependency=/home/daniel/git/testinghypermine/hypermine/target/release/deps --extern anyhow=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libanyhow-43ee26a5a4ae988b.rmeta --extern ash=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libash-1d91e46c2845798f.rmeta --extern ash_window=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libash_window-e581873b9287fab4.rmeta --extern common=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libcommon-bdb19128efefc960.rmeta --extern directories=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libdirectories-4a64043fdaf679b9.rmeta --extern downcast_rs=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libdowncast_rs-be493f8657cf1f42.rmeta --extern futures_util=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libfutures_util-79782c1686738829.rmeta --extern fxhash=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libfxhash-aa9a2aa3814ab312.rmeta --extern gltf=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libgltf-ae78e76833b667c0.rmeta --extern hdrhistogram=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libhdrhistogram-1c5607d77dc61a11.rmeta --extern hecs=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libhecs-7ceed138b109be7b.rmeta --extern lahar=/home/daniel/git/testinghypermine/hypermine/target/release/deps/liblahar-c3a4855d049ab353.rmeta --extern memoffset=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libmemoffset-33ada65033678cfe.rmeta --extern metrics=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libmetrics-79a79d3342c8701e.rmeta --extern metrics_core=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libmetrics_core-7e657b2c60f0cf49.rmeta --extern na=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libnalgebra-615d0479649eed30.rmeta --extern png=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libpng-089627b494fcd05d.rmeta --extern quinn=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libquinn-a3a9da2f9e31ef73.rmeta --extern rcgen=/home/daniel/git/testinghypermine/hypermine/target/release/deps/librcgen-be435a028b2dfce2.rmeta --extern rustls=/home/daniel/git/testinghypermine/hypermine/target/release/deps/librustls-825cbe6b34245b7a.rmeta --extern serde=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libserde-fbaa982a65688d4a.rmeta --extern server=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libserver-8fd1f97c8fb84524.rmeta --extern tokio=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libtokio-aa180fba706594fa.rmeta --extern toml=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libtoml-f7f6190f25a07c8e.rmeta --extern tracing=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libtracing-e0bf1ed6aae74c7d.rmeta --extern vk_shader_macros=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libvk_shader_macros-d2fb7777a36a4f79.rmeta --extern webpki=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libwebpki-66dacb92629212d5.rmeta --extern whoami=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libwhoami-05f81a39d2c15f6f.rmeta --extern winit=/home/daniel/git/testinghypermine/hypermine/target/release/deps/libwinit-0172bcc7807380da.rmeta -L native=/home/daniel/git/testinghypermine/hypermine/target/release/build/ring-86d47ac3f01cd916/out
     Building [=====================================================> ] 255/257: client
```

The issue seems to be related to the nightly toolchain, as it does not occur with the stable toolchain. The `rustup` output indicates that the nightly toolchain version is `1.48.0-nightly (8777a6b1e 2020-09-15)`.

## Conclusion

The issue with the build process hanging when using Rust's nightly toolchain on Linux appears to be related to an internal rustc issue, possibly caused by an upgrade of LLVM 11. Further investigation is needed to identify the root cause and potential solutions for this problem.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/76980)