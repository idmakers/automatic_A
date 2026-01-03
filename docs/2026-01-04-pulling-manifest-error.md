---
title: "Solving Ollama Pull Manifest Error"
tags:
  - ollama
  - pull manifest error
---

# Solving Ollama Pull Manifest Error

## Core Problem
The ollama run command is failing with an error message indicating that the maximum number of retries has been exceeded due to an unexpected EOF (End Of File), and subsequent attempts are failing with a "pull model manifest: file does not exist" error.

## Solution & Analysis
To resolve this issue, we need to ensure that the required system resources are available for the ollama run command. The suggested solution is to allocate sufficient memory on the system, particularly 32GB of RAM or more, as recommended by the developer who reported success with a similar setup.

```bash
# Check available memory and free space
free -m
df -h

# Allocate sufficient memory for ollama run
export OLLAMA_MEMORY=32G

# Run ollama again with the updated configuration
ollama run dolphin-mixtral:latest
```

Additionally, ensure that there is enough free space on the hard drive to accommodate the download.

## Conclusion
By allocating sufficient system resources and ensuring adequate free space on the hard drive, users can resolve the pull manifest error when running ollama with the `dolphin-mixtral:latest` image. Regularly check system performance and storage capacity to prevent similar issues in the future.

## Reference
- [Source](https://github.com/ollama/ollama/issues/1770)