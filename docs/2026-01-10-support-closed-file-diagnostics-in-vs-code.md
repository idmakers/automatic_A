---
title: "Support Closed-File Diagnostics in VS Code"
tags:
  - TypeScript
  - VS Code
---

# Support Closed-File Diagnostics in VS Code

## Core Problem

Project wide diagnostics are a highly requested feature for VS Code, especially for JavaScript and TypeScript projects. However, the single-threaded nature of the TS server prevents this from being implemented seamlessly. This limitation can interrupt normal user operations like completions.

## Solution & Analysis

To address this issue, we need to find an API that provides inverse dependencies (files that need to be rechecked after a file changes) for a given file. The `compileOnSaveAffectedFileList` command seems promising, but it requires `compileOnSave` to be enabled, which is not ideal for users who don't have this feature enabled.

We can use the `geterrForProject` command with some modifications to get around this limitation. However, this approach has its drawbacks, especially for larger projects where explicit file openings become necessary.

To improve this situation, we need a more efficient API that provides project-wide error reporting even when `compileOnSave` is not enabled. This can be achieved by using a separate diagnostics-only server.

### Code Example: Creating a Diagnostics-Only Server

We can create a new settings.json file in the `.vscode` directory with the following configuration:
```json
{
  "typescript.diagnosticsServer": {
    "startOnOpen": false,
    "openInNewProcess": true
  }
}
```
This will spin up a new TS server for diagnostics, which can be used to get inverse dependencies for files.

### Code Example: Using the `compileOnSaveAffectedFileList` Command

We can use the `compileOnSaveAffectedFileList` command to get a list of affected files. However, this command requires `compileOnSave` to be enabled.
```typescript
import * as vscode from 'vscode';
import { compileOnSaveAffectedFileList } from 'typescript';

function getAffectedFiles(file: vscode.Uri): Promise<string[]> {
  return new Promise((resolve) => {
    const affectedFiles = compileOnSaveAffectedFileList();
    resolve(affectedFiles.filter((file) => file.path.startsWith(file.path)));
  });
}
```
This code example demonstrates how to use the `compileOnSaveAffectedFileList` command to get a list of affected files.

## Conclusion

Implementing project-wide diagnostics in VS Code requires an efficient API that provides inverse dependencies for files. By using a separate diagnostics-only server and modifying existing commands, we can achieve this feature without relying on `compileOnSave`. This solution improves the overall user experience and makes it easier to implement project-wide error reporting.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/36664)