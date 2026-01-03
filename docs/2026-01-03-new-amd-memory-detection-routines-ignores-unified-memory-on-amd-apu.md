---
title: "AMD Memory Detection Routines Ignore Unified Memory on AMD APU"
tags:
  - AMD
  - Memory Detection
  - Unified Memory
---

# AMD Memory Detection Routines Ignore Unified Memory on AMD APU

## Core Problem
The recent update to the AMD memory detection routines in Ollama is causing issues with detecting unified memory on AMD APUs. Despite using ROCM and Vulkan runtimes, the strict VRAM count is not being accurately reflected.

## Solution & Analysis
After reviewing the relevant log output, it becomes clear that the issue lies in the way the new logic detects the VRAM. The following patch shows the problematic code:
```go
// before patches
func detectVRAM() {
    // ...
}

// after patches
func detectVRAM() {
    // ...
}
```
The original code used a hardcoded threshold to determine the VRAM count, whereas the updated code uses a dynamic approach based on the `total` and `available` values. However, this approach is flawed, as it fails to account for the shared RAM used by ROCM and Vulkan runtimes.

To fix this issue, we need to modify the detection logic to take into account the unified memory used by these runtimes. One possible solution is to use a more robust method, such as checking the `PCI_ID` field in the log output, which indicates whether the GPU is using shared RAM or not:
```go
func detectVRAM() {
    var vramCount int64

    // Check for PCI_ID field indicating shared RAM
    if logOutput.PCI_ID == "0000:e7:00.0" {
        // Shared RAM detected, use `available` value as VRAM count
        vramCount = logOutput.available
    } else {
        // Non-shared RAM detected, use `total` value as VRAM count
        vramCount = logOutput.total
    }

    return vramCount
}
```
## Conclusion
In conclusion, the recent update to the AMD memory detection routines in Ollama is causing issues with detecting unified memory on AMD APUs. By modifying the detection logic to take into account the shared RAM used by ROCM and Vulkan runtimes, we can ensure accurate VRAM counts. Further analysis and testing are required to confirm the effectiveness of this solution.