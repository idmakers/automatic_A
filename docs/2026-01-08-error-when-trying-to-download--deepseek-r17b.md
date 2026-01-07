---
title: "Error When Downloading DeepSeek-R1:7b with Ollama"
tags:
  - ollama
  - deepseek-r1
  - downloading
---

# Error When Downloading DeepSeek-R1:7b with Ollama

## Core Problem
When trying to download the `deepseek-r1:7b` model using Ollama, users encounter an error message indicating that a connection could not be made because the target machine actively refused it.

## Solution & Analysis
To resolve this issue, try different ports by setting the `OLLambda_HOST` environment variable. Here's how:

### Environment Variable Solution

```bash
export OLLAMA_HOST=http://localhost:8080
```

Then restart the Ollama service using one of the following methods:

**Service-based approach (Linux):**

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

**Windows approach:**

Restarting the Ollama service manually can be done by searching for "Ollama Service" in the Windows Services Manager, right-clicking on it, and selecting "Restart".

### Alternative Port Solution

If the above solution doesn't work, try using a different port. This can be done by modifying the `config.json` file located in the Ollama installation directory.

```json
{
  "ollama": {
    "host": "localhost",
    "port": 8081
  }
}
```

Restarting the Ollama service after making these changes will allow you to download the model using the updated port.

## Conclusion
By setting the `OLLambda_HOST` environment variable or modifying the `config.json` file, users can resolve the error and successfully download the `deepseek-r1:7b` model with Ollama.

## Reference
- [Source](https://github.com/ollama/ollama/issues/8565)