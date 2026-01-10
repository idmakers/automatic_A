---
title: "Supporting Closed-File Diagnostics in VS Code"
tags:
  - TypeScript
  - VS Code
  - Diagnostics
---

# Supporting Closed-File Diagnostics in VS Code

## Core Problem
Project-wide diagnostics are a highly requested feature for VS Code's JavaScript/TypeScript support, but the TS server's single-threaded nature poses a significant challenge. This limitation can interrupt normal user operations like code completions, hindering effective project-wide error reporting.

## Solution & Analysis
To overcome this limitation, we've explored various solutions, including using tasks to run diagnostics in separate processes. However, this approach is not widely adopted due to its complexity and overhead. Instead, we've introduced a new setting that spins up a dedicated TS Server for diagnostics, as demonstrated by the commit `f0942786b4ee338f02449e2e5751091e028eb898`. This setup enables project-wide error reporting without relying on tasks or the `compileOnSave` feature.

### API: Inverse Dependencies

To improve performance and scalability, we're introducing an API that returns a list of inverse dependencies for a given file. Specifically, this API will provide a list of files that need to be rechecked after changes to the original file. We believe this API is similar to `compileOnSaveAffectedFileList`, but with some key differences.

The proposed API will allow users to retrieve the list of affected files without relying on the `compileOnSave` feature, enabling project-wide error reporting even when `compileOnSave` is disabled.

### Code Example
```typescript
// ts-diagnostics.ts

interface InverseDependencies {
  [filePath: string]: string[];
}

async function getInverseDependencies(filePath: string): Promise<InverseDependencies> {
  const tsServerUrl = getTServerUrl();
  const response = await fetch(`${tsServerUrl}/diagnostics/inverse-dependencies`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ filePath }),
  });

  if (!response.ok) {
    throw new Error(`Failed to retrieve inverse dependencies for ${filePath}`);
  }

  const data = await response.json();
  return data.inverseDependencies;
}

// ts-diagnostics.ts (continued)
```
### Using the API

To use this API, simply call the `getInverseDependencies` function and pass the file path as an argument. The function will return a promise that resolves to an object containing the list of inverse dependencies for the given file.

```javascript
const filePath = 'path/to/file.ts';
getInverseDependencies(filePath)
  .then((inverseDependencies) => {
    console.log(inverseDependencies); // Output: { file1.ts: ['file2.ts', 'file3.ts'] }
  })
  .catch((error) => {
    console.error(error);
  });
```
## Conclusion
By introducing an API that returns inverse dependencies for a given file, we're providing a more efficient and scalable solution for project-wide diagnostics in VS Code. This API allows users to retrieve the list of affected files without relying on tasks or the `compileOnSave` feature, enabling effective project-wide error reporting even when these features are disabled.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/36664)