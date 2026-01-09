---
title: "Enabling TypeScript Type Checking in JavaScript Projects with Inline Comments"
tags:
  - Typescript
  - JavaScript
  - Inline Comments
---

# Enabling TypeScript Type Checking in JavaScript Projects with Inline Comments

## Core Problem
Typescript's primary goal is to be Javascript + Types, but many projects are written in plain Javascript and still benefit from a type checker. Currently, types from JSdoc comments can be parsed by Typescript, but it would be more convenient to annotate existing Javascript code with types without the need for an emit stage.

## Solution & Analysis
Typescript's language service is a significant advantage here, as it allows on-the-fly error checking and code completion. To achieve this functionality, inline type annotations using Flow comments can be used. The syntax for Flow comments is `_:_ [type]` or `_:: [tscode]`.

Here's an example of how to use Flow comments in a Typescript class:

```typescript
/// <reference path="..." />

class Hello {
    /*:: private*/ hello(message /*: string*/) /*: Promise<{}>*/{
        const promise /*: Promise<void>*/ = null 
    return /*:: <Promise<{}>>*/ null;
}
```

This approach allows for the same code to be read by both Browser and Node.js without the need for source mapping or a separate transpilation step. Additionally, since the types are included in the source file, there is no need for a separate typings installation for Node_modules written in this format.

## Conclusion
Enabling TypeScript type checking in existing Javascript projects using inline comments provides a convenient solution for developers who want to leverage Typescript's benefits without having to refactor their entire codebase. With Flow comments and Typescript's language service, on-the-fly error checking and code completion are now available for annotated Javascript files.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/9694)