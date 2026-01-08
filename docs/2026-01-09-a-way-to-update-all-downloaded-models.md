---
title: "Automate Model Updates for Ollama Models"
tags:
  - ollama
  - model updates
  - automation
---

# Automate Model Updates for Ollama Models

## Core Problem
Current workflow requires manual update of individual models, leading to inefficiencies and potential human error.

## Solution & Analysis
### Updating Multiple Models at Once with a Single Command

You can update all downloaded models using the following bash script:

```bash
#!/bin/bash

ollama list | tail -n +2 | awk '{print $1}' | while read -r model; do
  ollama pull $model
done
```

This script leverages `ollama`'s command-line interface to:
- List all models (`ollama list`)
- Skip the first line of output (header) with `tail -n +2`
- Extract the model names from the remaining lines with `awk '{print $1}'`
- Iterate over each model name using a `while` loop
- Pull the latest version of each model with `ollama pull`

## Conclusion
By automating the update process for all downloaded models, users can streamline their workflow and reduce the likelihood of human error.

## Reference
- [Source](https://github.com/ollama/ollama/issues/1890)