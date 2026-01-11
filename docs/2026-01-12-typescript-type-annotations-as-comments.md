---
title: "Enabling Real-Time Type Checking in JavaScript Projects with TypeScript Annotations"
tags:
  - TypeScript
  - Type Checking
  - Code Completion
---

# Enabling Real-Time Type Checking in JavaScript Projects with TypeScript Annotations

## Core Problem
The existing JavaScript projects can benefit from the excellent type checking and language services provided by TypeScript, but they lack the emit stage. This makes it challenging to leverage these features without modifying or rewriting the entire project.

## Solution & Analysis
TypeScript's goal is simply to be "Javascript + Types." There are several ways to achieve this, including using allowJS for projects already written in JavaScript and utilizing JSDoc comments for parsing types. However, annotating JavaScript code with types directly would provide a significant boost to existing JavaScript projects.

To enable real-time type checking, code completion, and language server awesomeness in JavaScript projects, we can utilize TypeScript annotations as comments. This approach allows us to leverage the power of TypeScript without requiring any modifications or rewriting the entire project.

### Example: Annotated JavaScript Code

```javascript
/// <reference path="..." />

class Hello {
    /*:: private*/ hello(message /*: string*/) /*: Promise<{}>*/{
                const promise /*: Promise<void>*/ = null 
        return /*:: <Promise<{}>>*/ null;
    }
}
```

In this example, we use the `_:` comment to annotate the `hello` function with its type parameters. The `/*:: */` syntax is used to indicate that the types are provided as comments.

### Top Solution: Flow Comments
The top solution for enabling real-time type checking in JavaScript projects is using Flow comments. Flow has a well-defined spec and was a major feature request on their end.

```javascript
_:_ [type]
```

This comment syntax allows us to annotate our code with types, which can be used by Flow's language service to provide real-time error checking and code completion.

### Benefits of TypeScript Annotations

Using TypeScript annotations as comments provides several benefits, including:

*   No source mapping needed: The browser or Node.js reads the same code, eliminating the need for source mapping.
*   No transpiling step required: We can annotate our existing JavaScript codebase without waiting for a transpiling step.
*   Simplified dependency management: With source and type annotations in the same file, we don't need to install separate typings.

## Conclusion
Enabling real-time type checking in JavaScript projects with TypeScript annotations is a game-changer. By annotating our code with types using comments, we can leverage the power of TypeScript without modifying or rewriting our entire project. This approach provides several benefits, including simplified dependency management and no source mapping or transpiling required.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/9694)