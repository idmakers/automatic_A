---
title: "Getting Started with Meson/Bug Reports in pandas"
tags:
  - pandas
  - meson
  - bug reports
---

# [Use the Title]

## Core Problem
The pandas team is transitioning to a new build system, meson, which addresses issues with the current setuptools-based build system. This change aims to improve the build process and simplify code maintenance.

## Solution & Analysis
To get started with meson builds in pandas, follow these steps:

### Step 1: Create a Conda Environment

```bash
# Create a new conda environment or update an existing one
conda env create --file=environment.yml
```

### Step 2: Install Dependencies

```bash
# Install the oldest-supported-numpy version (required for no-build-isolation mode)
pip install oldest-supported-numpy>=0.10
```

### Step 3: Build the Project with Meson

```bash
# Install the project using meson
pip install . -ve --no-build-isolation --config-settings="builddir=builddir"
```

Optional:

* For debugging:
```bash
# Install the project with debug settings
pip install . -ve --no-build-isolation --config-settings builddir="builddir" --config-settings setup-args="-Dbuildtype=debug"
```

### Step 4: Test the Project

```python
# Run tests using pytest
pytest pandas
```

Note: If you built pandas in editable mode, you don't need to rebuild the C extensions every time you change a C/Cython file. It will now be automatically rebuilt on import.

## Conclusion
By following these steps, you can get started with meson builds in pandas and take advantage of improved build performance and simplified code maintenance.

## Reference
- [Source](https://github.com/pandas-dev/pandas/issues/49683)