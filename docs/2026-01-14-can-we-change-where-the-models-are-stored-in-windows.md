---
title: "Customizing Ollama Model Storage in Windows"
tags:
  - ollama
  - model storage
  - environment variables

---

# Customizing Ollama Model Storage in Windows

## Core Problem

The Ollama models are automatically downloaded to C:/Users/username/.ollama by default, which may not be a suitable location for everyone due to storage issues.

## Solution & Analysis

To change the directory where the models are stored, you can use the `OLLAMA_MODELS` environment variable. This allows you to specify an alternative location for the model files.

```bash
# Set the OLLAMA_MODELS environment variable to a custom directory
export OLLAMA_MODELS=/path/to/custom/directory/.ollama
```

You can also set this variable in your operating system's settings or using a shell configuration file (e.g., `~/.bashrc`).

```bash
# Set the OLLAMA_MODELS environment variable in bash
export OLLAMA_MODELS=/path/to/custom/directory/.ollama
```

## Conclusion

By setting the `OLLAMA_MODELS` environment variable, you can customize the location where Ollama models are stored, providing more flexibility and control over model storage.

## Reference
- [Source](https://github.com/ollama/ollama/issues/2551)