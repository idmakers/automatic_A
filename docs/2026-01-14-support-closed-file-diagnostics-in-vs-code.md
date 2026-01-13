---
title: "Support closed-file diagnostics in VS Code"
tags:
  - TypeScript
  - VS Code
  - Diagnostics
---

# Supporting Closed-File Diagnostics in VS Code

## Core Problem
Project-wide diagnostics are a highly requested feature in VS Code for JavaScript and TypeScript projects. However, the current implementation of the TypeScript Server is single-threaded, which prevents it from computing diagnostics without interrupting normal user operations like code completions.

To address this limitation, the VS Code team has explored alternative solutions, including using tasks to run diagnostics in a separate process. Although tasks can provide a way to run diagnostics, few users actually use them due to the added complexity.

## Solution & Analysis
To improve the diagnostic experience for TypeScript projects, we need an API that provides inverse dependencies for files, allowing us to detect changes without triggering a huge number of events for larger projects like the VS Code source. The `compileOnSaveAffectedFileList` command already exists and can be used to achieve this, but it requires compile-on-save to be enabled.

To enable project-wide error reporting even when compile-on-save is not enabled, we need to implement an API that provides similar functionality without relying on compile-on-save.

### Code Example: Inverse Dependencies API
```typescript
interface InverseDependencies {
  [filePath: string]: string[];
}

const getInverseDependencies = (filePath: string): Promise<InverseDependencies> => {
  // Implement the logic to compute inverse dependencies for a given file path
  // For example, using the `compileOnSaveAffectedFileList` command
  return fetch(`http://localhost:5000/compileOnSaveAffectedFileList?filePath=${filePath}`)
    .then(response => response.json())
    .then(data => data.inverseDependencies);
};
```
### Code Example: Using the Inverse Dependencies API in VS Code
```typescript
const vscode = require('vscode');

function getErrorsForFile(filePath: string): Promise<void> {
  return getInverseDependencies(filePath).then(inverseDependencies => {
    // Use the inverse dependencies to compute errors for the file
    // For example, using the `geterr` command with an updated open document
    const openDocument = vscode.workspace.openTextDocument(filePath);
    const error = await vscode.commands.executeCommand('typescript.geterr', openDocument.uri.toString());
    // Update the open document to reflect changes
    await vscode.commands.executeCommand('typescript.updateOpen', openDocument.uri.toString(), inverseDependencies);
  });
}
```
## Conclusion
In this solution, we implement an API that provides inverse dependencies for files, allowing us to detect changes without triggering a huge number of events. By using this API and implementing the necessary logic in VS Code, we can provide project-wide error reporting even when compile-on-save is not enabled, improving the overall diagnostic experience for TypeScript projects.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/36664)