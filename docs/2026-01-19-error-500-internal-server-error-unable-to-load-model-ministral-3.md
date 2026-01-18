---
title: "Error: 500 Internal Server Error: Unable to Load Model - Ollama Issue"
tags:
  - ollama
  - internal server error
  - model loading issue
---

# Error: 500 Internal Server Error: Unable to Load Model - Ollama Issue

## Core Problem
Users are experiencing an internal server error (500) when attempting to load a specific model using the Ollama library on Linux Manjaro.

## Solution & Analysis
### Step 1: Verify Ollama Version
Ensure that the Ollama version is up-to-date by running `ollama --version`. The current version should be 0.13.1.

```bash
$ ollama --version
ollama version is 0.13.1
```

### Step 2: Check Model Manifest
Verify that the model manifest is correct and exists in the expected location.

```bash
$ ollama run hf.co/unsloth/Ministral-3-8B-Reasoning-2512-GGUF:Q4_K_XL --verbose
```

This command will pull the required manifests for the model, including the `381f6e188ec2` and `00300cf53173` models.

### Step 3: Investigate Model Loading Issues
Check the log output for any errors or issues related to model loading. The provided log output shows that both the `381f6e188ec2` and `00300cf53173` models are pulling correctly, but an error occurs when attempting to load the `Ministral-3-14B-Reasoning-2512-GGUF:Q4_K_XL` model.

```bash
pulling manifest 
pulling 381f6e188ec2: 100% ▕████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████

## Reference
- [Source](https://github.com/ollama/ollama/issues/13321)