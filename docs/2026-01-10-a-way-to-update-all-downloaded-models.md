---
title: "Automate Model Updates with Batch Pulling"
tags:
  - model updates
  - automation
---

# Automate Model Updates with Batch Pulling

## Core Problem
Currently, users have to manually pull each model separately, which can be time-consuming and prone to human error.

## Solution & Analysis
### Batch Pulling Script

You can update all models using a batch pulling script:

```bash
#!/bin/bash

# List all available models (excluding the first one)
ollama list | tail -n +2 | awk '{print $1}' | while read -r model; do
  # Pull each model
  ollama pull $model
done
```

This script uses `tail` to exclude the first model from the list, then pipes the output to `awk` to extract only the model names. The resulting list is then processed by a `while` loop that pulls each model individually using the `ollama pull` command.

### Benefits

*   Automates the process of pulling all models
*   Reduces manual effort and minimizes human error
*   Can be scheduled for periodic updates

## Reference
- [Source](https://github.com/ollama/ollama/issues/1890)