---
title: "TSServer Exited. Code: null. Signal: SIGTERM - A Troubleshooting Guide"
tags:
  - TypeScript
  - VSCode
  - TSC Server
  - Debugging
---

# TSServer Exited. Code: null. Signal: SIGTERM - A Troubleshooting Guide

## Core Problem
When launching a TypeScript project in Visual Studio Code (VSCode), even on an empty project, the TSServer exited with a code of null and a signal of SIGTERM. This issue persists despite restarting the TSC server.

## Solution & Analysis

To troubleshoot this issue, we will follow these steps:

1. **Enable Debugging**: Ensure that debugging is enabled in VSCode by checking the `launch.json` file in your project directory.
2. **Disable typings Installer**: Try disabling the typings installer to see if it's related to the issue. You can do this by setting `"typescript.disableAutomaticTypeAcquisition": true` in your `tsconfig.json` file.

```json
{
    "compilerOptions": {
        // ...
        "disableAutomaticTypeAcquisition": true,
        // ...
    }
}
```

3. **Run with Extensions Disabled**: Try running the project with all extensions disabled to see if the issue persists.
4. **Verify TSC Server Logs**: Check the TSC server logs for any errors or warnings that might indicate the cause of the issue.

```json
{
    "compilerOptions": {
        // ...
    }
}
```

5. **Check for Updates**: Ensure that VSCode and TypeScript are up-to-date, as newer versions may fix this issue.
6. **Reset TSC Server Settings**: Try resetting the TSC server settings to their default values.

```json
{
    "compilerOptions": {
        // ...
        "resetConfig": true,
        // ...
    }
}
```

## Conclusion
By following these troubleshooting steps, you should be able to identify and potentially fix the issue causing the TSServer to exit with a code of null and signal SIGTERM. If none of these solutions work, please provide more detailed information about your project and environment, as this will help us further investigate the issue.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/62337)