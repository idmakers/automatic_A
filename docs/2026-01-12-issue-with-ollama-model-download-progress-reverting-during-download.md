---
title: "Ollama Model Download Issue with Reverting Progress"
tags:
  - ollama-model-download
  - progress-reverting
  - macos
  - gpu-apple
  - cpu-apple
  - ollama-version-0.5.7
---

# Ollama Model Download Issue with Reverting Progress

## Core Problem
The Ollama model download process often experiences a problem where the progress reverts, sometimes after 10-12% or even after 60%. The total download size also decreases before continuing. This issue has been observed on macOS with Apple GPU and CPU.

## Solution & Analysis
To resolve this issue, we need to investigate possible causes and provide a solution.

### Possible Causes

*   Insufficient bandwidth: Inadequate network speed might cause the download process to revert progress.
*   Corrupted model file: A damaged or incomplete model file can lead to inconsistent downloads.
*   GPU issues: The Apple GPU might be experiencing hardware-related problems, affecting the download process.

### Solution

To resolve the issue, try the following steps:

#### Step 1: Verify Model Integrity

Before downloading a model, verify its integrity using tools like `sha256sum` or `md5sum`.

```bash
# Check model hash (macOS)
sha256sum ollama_model.zip
```

#### Step 2: Update Ollama Version

Update to the latest version of Ollama to ensure you have the most recent bug fixes and features.

```bash
# Update ollama using pip
pip install --upgrade ollama
```

#### Step 3: Increase Bandwidth

Increase your network bandwidth by restarting your router or switching to a faster connection.

#### Step 4: Monitor Download Process

Monitor the download process closely to detect any signs of progress reversion. Use tools like `htop` or `top` to track system resources during the download.

```bash
# Install htop (macOS)
brew install htop
```

### Conclusion
The issue with Ollama model downloads experiencing reverted progress can be resolved by verifying model integrity, updating to the latest version of Ollama, increasing network bandwidth, and monitoring the download process closely. By following these steps, you should be able to successfully download models without any issues.

## Reference
- [Source](https://github.com/ollama/ollama/issues/8484)