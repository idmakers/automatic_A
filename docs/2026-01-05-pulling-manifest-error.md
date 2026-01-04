---
title: "Resolving Pull Manifest Errors in Ollama"
tags:
  - ollama
  - manifest errors
---

# Resolving Pull Manifest Errors in Ollama

## Core Problem
When running `ollama run dolphin-mixtral:latest` for the first time, users may encounter an error message indicating "max retries exceeded: unexpected EOF" or "Error: pull model manifest: file does not exist". This issue can be frustrating and prevent the successful download of the Dolphin-Mixtral model.

## Solution & Analysis
To resolve this problem, we need to ensure that your system has sufficient resources and free space. Here's a step-by-step guide:

### Verify System Resources

*   Check your computer's CPU and GPU memory: Ensure you have at least 32GB of RAM on your MacStation, as recommended by the developer.
*   Monitor your hard drive space: Free up enough disk space to accommodate the model download.

```bash
# Check available memory (in GB)
total_memory=$(free -m | awk '/^Mem:/ {print $2}' | sed 's/K//g')
available_memory=$((total_memory * 1024 / 1024))

echo "Available Memory: $available_memory GB"
```

### Optimize Disk Space

*   Remove any unnecessary files and data from your hard drive.
*   Consider upgrading to a larger storage device if needed.

```bash
# Display disk space usage in GB
df -h | awk '/^\/dev\// {print \$5}' | sed 's/G//g'
```

### Adjust Ollama Configuration

*   Update the `ollama.json` file with your preferred model version and system configuration.
*   Set the "pull manifest" option to true in the configuration file.

```json
{
  "models": {
    "dolphin-mixtral:latest": {
      "url": "https://example.com/model",
      "manifest": true,
      "config": {
        "pull_manifest": true
      }
    }
  }
}
```

### Restart Download

After configuring the `ollama.json` file and verifying system resources, restart the download process using the following command:

```bash
ollama run dolphin-mixtral:latest --manifest
```

## Conclusion
By following these steps, you should be able to resolve the pull manifest error in Ollama. Ensure your system has sufficient resources and free space, optimize disk space as needed, adjust the `ollama.json` configuration file, and restart the download process. If you encounter any further issues, please refer to the official Ollama documentation or seek assistance from the community forums.

## Reference
- [Source](https://github.com/ollama/ollama/issues/1770)