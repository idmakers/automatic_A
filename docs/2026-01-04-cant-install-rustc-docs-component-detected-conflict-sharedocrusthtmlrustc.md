---
title: "Can't Install rustc-docs Component: Resolving the Conflict"
tags:
  - rust-lang
  - installation
  - rustc-docs

---

# Can't Install rustc-docs Component: Resolving the Conflict

## Core Problem

The `rustc-docs` component cannot be installed on Rust due to a detected conflict. The error message indicates that there is an issue with the directory structure, specifically the overlap between `share/doc/rust/html/rustc` and `rustc-docs`. This problem persists despite the fix mentioned in GitHub pull request #75593.

## Solution & Analysis

```bash
# Check the current installation of rustc-docs
info: downloading component 'rustc-docs'
info: installing component 'rustc-docs'
info: Defaulting to 500.0 MiB unpack ram  
  9.9 MiB /   9.9 MiB (100 %)   2.9 MiB/s in  3s ETA:  0s
info: rolling back changes
error: failed to install component: 'rustc-docs-x86_64-unknown-linux-gnu', detected conflict: '"share/doc/rust/html/rustc"'
```

To resolve this issue, you can try the following solutions:

*   Rename the `rustc` directory inside `share/doc/rust` to avoid conflicts:
    ```bash
sudo mv share/doc/rust/html/rustc share/doc/rust/html/rustc-renamed
```
*   Create a symbolic link from `rustc-docs` to `rustc` instead of installing it separately:
    ```bash
ln -s share/doc/rust/html/rustc share/doc/rustc/docs
```

## Conclusion

By renaming the conflicting directory or creating a symbolic link, you can resolve the conflict and successfully install the `rustc-docs` component. Keep in mind that these workarounds may have unintended consequences on your system's file structure. Always be cautious when modifying system files to avoid data loss or corruption.

## Reference
- [Source](https://github.com/rust-lang/rust/issues/75833)