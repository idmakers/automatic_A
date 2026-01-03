---
title: "Resolving 'max retries exceeded' and 'file does not exist' Errors When Pulling Manifest"
tags:
  - ollama
  - docker
  - manifest
  - errors
---

# Resolving 'max retries exceeded' and 'file does not exist' Errors When Pulling Manifest

## Core Problem
The "ollama run" command fails with an error message indicating that the maximum number of retries has been exceeded due to an unexpected EOF (End Of File), followed by a failure to pull the model manifest, resulting in a file not existing error. This issue can be frustrating for users trying to deploy and train machine learning models.

## Solution & Analysis
To resolve this issue, we need to ensure that the system has sufficient memory resources to handle the Docker container's requirements. The recommended minimum memory requirement for running ollama is 32GB of CPU and GPU memory on a Macstation. Additionally, it's crucial to have enough free space on the hard drive.

### Solution Steps:

1. **Check System Resources**:
   * Check your system's CPU and GPU memory usage.
   * Ensure you have at least 32GB of total memory available (CPU + GPU).
2. **Update Docker and ollama Images**:
   * Run `docker pull --update docker/ollama:latest` to update the ollama image.
3. **Clear Download Directory**:
   * Remove any existing download directory or cache files related to ollama.
4.  **Increase Memory Allocation for Docker Container**:
    * Run the command with increased memory allocation, e.g., `OLLAMA_MEMORY=64G ollama run dolphin-mixtral:latest`
5.  **Check Disk Space Availability**:
    * Ensure there is sufficient free space on your hard drive (at least a few GB).

### Example Code:

```bash
# Increase memory allocation for Docker container
OLLAMA_MEMORY=64G ollama run dolphin-mixtral:latest

# Clear download directory
rm -rf ~/.ollama/download/

# Update Docker and ollama images
docker pull --update docker/ollama:latest
```

## Conclusion
By following these steps, you should be able to resolve the 'max retries exceeded' and 'file does not exist' errors when pulling manifest. Ensure your system has sufficient memory resources and disk space available for optimal performance.

## Reference
- [Source](https://github.com/ollama/ollama/issues/1770)