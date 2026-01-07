---
title: "ModuleNotFoundError: No module named 'langchain.schema'"
tags:
  - langchain
  - Python
  - ModuleNotFoundError

---

# Resolving the ModuleNotFoundError: No module named 'langchain.schema'

## Core Problem
The error `ModuleNotFoundError: No module named 'langchain.schema'` occurs when Python is unable to locate the `'langchain.schema'` module. This could be due to a few reasons, including an incorrect installation of the LangChain package or issues with the package itself.

## Solution & Analysis

### Step 1: Verify Installation
To resolve this issue, first verify that the LangChain package is installed in your current Python environment. You can do this by running `pip show langchain`. If it's not installed, you can install it using `pip install langchain==0.0.20`.

```bash
pip show langchain
```

If the package is not found, proceed to the next step.

### Step 2: Check Package Location
Python might be looking in the wrong place for the LangChain package. If you have multiple Python environments and the one you're using doesn't have LangChain installed, try switching to a different environment or installing the package in that environment.

```bash
# Install langchain in the current environment
pip install langchain==0.0.20
```

### Step 3: Analyze Package Issues
There have been similar issues reported in the LangChain repository, including:

*   ['langchain' is not a package](https://github.com/langchain-ai/langchain/issues/2079)
*   [Cannot import name 'HumanMessage' from 'langchain.schema'](https://github.com/langchain-ai/langchain/issues/8527)
*   [Unable to import from langchain.document_loaders](https://github.com/langchain-ai/langchain/issues/2389)

These issues were often resolved by updating LangChain and other packages, or renaming a file named `langchain.py` in the project.

## Conclusion
The error `ModuleNotFoundError: No module named 'langchain.schema'` can be resolved by verifying the installation of the LangChain package, checking the package location, and analyzing package issues. If none of these steps resolve the issue, please provide more information about your setup to help diagnose the problem further.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/8843)