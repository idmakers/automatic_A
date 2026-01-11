---
title: "Mastering Meson Build Support in pandas"
tags:
  - pandas
  - meson
  - build system
---

# Mastering Meson Build Support in pandas

## Core Problem
The current setuptools-based build system in pandas has limitations, such as race conditions on parallel builds. The introduction of a new build system, meson, aims to address these issues and simplify the build process.

## Solution & Analysis

To use meson with pandas, follow these steps:

### Step 1: Create a New Conda Environment or Update the Existing One

```bash
conda env create --file=environment.yml
```

or

```bash
conda env update --file environment.yml --prune
```

Make sure `pandas-dev` is activated.

### Step 2: Install Oldest-Supported-Numpy

```bash
pip install oldest-supported-numpy>=0.10
```

This is necessary since pip will not auto-pull dependencies in no-build-isolation mode.

### Step 3: Build Command

```bash
pip install . -ve --no-build-isolation --config-settings="builddir=builddir"
```

If you want to debug, pass `--Ddebug=true`:

```bash
pip install . -ve --no-build-isolation --config-settings builddir="builddir" --config-settings setup-args="-Dbuildtype=debug"
```

### Step 4: Test Command

```python
pytest pandas
```

Note that if you built pandas in editable mode, the C extensions will be automatically rebuilt on import.

## Conclusion

With these steps, you can start using meson with pandas. As mentioned in the tracker thread, the goal is to complete the migration to meson by the next minor release (2.1).

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/49683)