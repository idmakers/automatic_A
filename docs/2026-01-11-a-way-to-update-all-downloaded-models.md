---
title: "Automate Model Updates with Ollama"
tags:
  - model-management
  - automation
---

# Automate Model Updates with Ollama

## Core Problem
Updating individual models within the Ollama framework can be time-consuming and prone to human error. Currently, users must pull each model separately, which can lead to inefficiencies in managing large collections of models.

## Solution & Analysis
### Batch Model Update Script

You can update all downloaded models using a single bash script:

```bash
#!/bin/bash

# Get the list of models (skipping the first entry)
ollama list | tail -n +2 | awk '{print $1}' | while read -r model; do
  # Pull each model individually
  ollama pull $model
done
```

This script leverages Ollama's `list` command to fetch a list of models, filters out the first entry (assuming it's not a model), and then uses an `awk` to extract the model names. The resulting list is then piped into a `while` loop, which iterates over each model and pulls it using Ollama's `pull` command.

### Benefits of Batch Updates

- **Efficiency**: Automating the update process saves time and reduces manual labor.
- **Consistency**: Ensuring all models are updated simultaneously maintains consistency across your project or organization.
- **Error Reduction**: Minimizing human involvement decreases the likelihood of errors during the update process.

## Conclusion
By utilizing a batch update script, you can efficiently manage your Ollama model collections and streamline your workflow. This approach not only saves time but also improves accuracy and consistency in your model updates.

## Reference
- [Source](https://github.com/ollama/ollama/issues/1890)