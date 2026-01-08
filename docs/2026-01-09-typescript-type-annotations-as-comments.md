---
title: "Enabling Real-Time Type Checking in JavaScript Projects with Comment Annotations"
tags:
  - TypeScript
  - Typescript type annotations as comments
  - Intellisense and Code Completion
---

# Enabling Real-Time Type Checking in JavaScript Projects with Comment Annotations

## Core Problem
TypeScript's primary goal is to be "Javascript + Types." However, there are many use cases where developers want to utilize the excellent type checker without requiring an emit stage. Existing projects written in JavaScript can benefit from allowJS, and TypeScript already supports parsing types from JSDoc comments. Nevertheless, annotating JavaScript code with types would greatly enhance the development experience by providing real-time type checking, intellisense, and language server awesomeness.

## Solution & Analysis
To address this issue, we'll explore the concept of using comment annotations to enable real-time type checking in JavaScript projects. We'll delve into the benefits and feasibility of this approach, including the role of TypeScript's language service.

### Annotating JavaScript with Types

TypeScript already supports parsing types from JSDoc comments. However, what would be incredibly useful is the ability to annotate JavaScript code directly with types without requiring any emit stage. This can be achieved using a syntax for comment annotations:

```
/_: [type] */
/_:: [tscode] */
```

This syntax allows developers to explicitly define types for their JavaScript code, enabling the TypeScript type checker to provide real-time feedback.

### Example Code

Here's an example of how this syntax can be used in practice:
```javascript
class Hello {
  /*:: private*/ hello(message /*: string*/) /*: Promise<{}>*/{
    const promise /*: Promise<void>*/ = null;
    return /*:: <Promise<{}>>*/ null;
  }
}
```
In this example, we've annotated the `hello` method with a type comment. The first line (`/*:: private*/`) indicates that the method is private. The second line (`hello(message /*: string*/)`) specifies the method's parameters and return type. The third line (`/*: Promise<void>*/`) annotates the `promise` variable, indicating its type.

### Benefits

The benefits of using this syntax are numerous:

*   **Real-time Type Checking**: With TypeScript's language service, developers can get real-time error checking and code completion as they write their code.
*   **Intellisense and Code Completion**: Annotating JavaScript with types enables intellisense and code completion for IDEs like VSCode.
*   **No Source Mapping Needed**: Since the code is annotated directly in the file, source mapping is not necessary. The browser or Node.js can read the same code.
*   **No Separate Typings Install Required**: With source+types being the same file, Node_modules written in this flavor don't need a separate typings install.

## Conclusion
Enabling real-time type checking in JavaScript projects with comment annotations represents a significant step forward for developers. By annotating their code directly with types, they can tap into the full potential of TypeScript's language service. This approach offers numerous benefits, including improved development experience, enhanced intellisense and code completion, and streamlined workflows.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/9694)