---
title: "DeepSeek-R1 671B Q4_K_M Error: Model Requires More System Memory Than Available"
tags:
  - ollama
  - DeepSeek-R1
  - MoE Architecture
---

# DeepSeek-R1 671B Q4_K_M Error: Model Requires More System Memory Than Available

## Core Problem
The DeepSeek-R1 671B model with Q4 quantization is not running on a system with enough RAM, despite the MoE architecture's supposed efficiency. The error message indicates that the model requires more system memory than is available.

## Solution & Analysis
To solve this issue, we need to understand why the model requires more RAM than expected. The problem statement explains that the MoE architecture reduces computation requirements, but model weights still need to be loaded for expert selection. A potential solution is to use [SwapMoE](https://aclanthology.org/2024.acl-long.363.pdf), which is not implemented in mainstream open-source inference engines.

Here's an example of how you can work around the "more system memory" restriction by creating free swap space or tricking ollama into thinking you have extra resources:

```bash
# Create a large swap file
sudo fallocate -l 1T /dev/shm/swapfile

# Set the swap file as active
sudo swapon /dev/shm/swapfile

# Add the following lines to your ollama configuration file (e.g., `~/.ollama/config.toml`)
[Ollama]
# ...

[Ollama]
# ...
SwapMoE = true
```

Alternatively, you can use mmap to make the model available:

```bash
# Map the model into memory using mmap
mmap -d 0 /dev/shm/model.bin --prot=write

# Add the following lines to your ollama configuration file (e.g., `~/.ollama/config.toml`)
[Ollama]
# ...
MMap = true
```

## Conclusion
In conclusion, running the DeepSeek-R1 671B model with Q4 quantization on a system with limited RAM can be challenging due to the MoE architecture's design. By understanding the problem and implementing solutions such as SwapMoE or mmap, you can work around the "more system memory" restriction and run the model successfully.

<!-- ADSENSE_PLACEHOLDER -->

## Reference
- [Source](https://github.com/ollama/ollama/issues/8667)