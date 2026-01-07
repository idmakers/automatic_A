---
title: "Bug in VSCode and Typescript - TSServer exited. Code: null. Signal: SIGTERM"
tags:
  - TypeScript
  - Visual Studio Code
  - Bug
---

# Bug in VSCode and Typescript - TSServer exited. Code: null. Signal: SIGTERM

## Core Problem

When launching a TypeScript project, even an empty one, the error "TSServer exited. Code: null. Signal: SIGTERM" appears in the output, despite restarting the TS server.

## Solution & Analysis

The issue is caused by a problem with the TypeScript Server (tsserver) extension in VSCode. The error message indicates that the tsserver process terminated abruptly due to a signal (SIGTERM).

To fix this issue:

1. Check if there are any unhandled exceptions related to the typings installer, as suggested in one of the top solutions.

```json
{
  "compilerOptions": {
    // ...
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.test.ts",
  ],
  "exclude": [
    "node_modules",
    "build",
  ]
}
```

2. Try setting `"typescript.disableAutomaticTypeAcquisition": true` to see if it stops the crash.

```json
{
  "compilerOptions": {
    // ...
    "disableAutomaticTypeAcquisition": true,
    // ...
  },
  // ...
}
```

3. Disable all extensions and try to reproduce the issue, as suggested in one of the top solutions.

```json
// disable all extensions

{
  "compilerOptions": {
    // ...
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.test.ts",
  ],
  "exclude": [
    "node_modules",
    "build",
  ]
}
```

4. If none of the above solutions work, try resetting VSCode to its default settings.

```json
// reset vscode to default settings

{
  "compilerOptions": {
    // ...
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.test.ts",
  ],
  "exclude": [
    "node_modules",
    "build",
  ]
}
```

## Conclusion

The error "TSServer exited. Code: null. Signal: SIGTERM" in VSCode and TypeScript can be caused by various factors, including problems with the tsserver extension or unhandled exceptions related to typings installer. Disabling extensions, setting `"typescript.disableAutomaticTypeAcquisition": true`, and resetting VSCode to its default settings may help resolve the issue.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/62337)