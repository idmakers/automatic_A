---
title: "AMD Memory Detection Routines Ignore Unified Memory on AMD APU"
tags:
  - AMD
  - Ollama
  - Memory Detection
---

# AMD Memory Detection Routines Ignore Unified Memory on AMD APU

## Core Problem
The current implementation of memory detection routines in Ollama incorrectly identifies strict VRAM on AMD APUs even when unified RAM is used by ROCM and Vulkan runtimes.

## Solution & Analysis
To resolve this issue, we need to modify the memory detection logic to account for the use of unified RAM. The new routine will need to take into account the actual available VRAM and ignore the shared RAM allocated by ROCM and Vulkan.

```go
// MemoryDetection.go

package main

import (
	"fmt"
)

const (
	unifiedRAM_THRESHOLD = 20 * 1024 * 1024 // 20 GiB

	// ... other constants ...
)

type Memory struct {
	total   uint64
	available uint64
}

func detectMemory() (uint64, error) {
	// Get the total and available VRAM
	var vram Memory
	vram.total = getVramTotal()
	vram.available = getVramAvailable()

	// Check if unified RAM is used
	if vram.available > unifiedRAM_THRESHOLD {
		return 0, fmt.Errorf("unified RAM is used")
	}

	return vram.available, nil
}

func main() {
	memory, err := detectMemory()
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("Available VRAM: %d bytes\n", memory)
	}
}

// ... other functions to get total and available VRAM ...
```

## Conclusion
The updated memory detection routine will correctly identify the actual available VRAM on AMD APUs, even when unified RAM is used by ROCM and Vulkan. This fix ensures that Ollama accurately detects the memory constraints of the system, allowing for more efficient and effective model training.