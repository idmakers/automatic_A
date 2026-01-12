---
title: "Enabling Type Checking on Existing JavaScript Projects with Comments"
tags:
  - TypeScript
  - JavaScript
  - Code Completion
---

# Enabling Type Checking on Existing JavaScript Projects with Comments

## Core Problem

TypeScript's primary goal is to add type checking to JavaScript, but this often requires a separate compilation step. However, for many existing JavaScript projects, the overhead of compiling and transpiling code may not be desirable.

## Solution & Analysis

### Introducing Type Annotations as Comments

To address this issue, we can utilize TypeScript's ability to parse types from JSDoc comments and add type annotations directly into our JavaScript codebase. This allows us to benefit from type checking without requiring a separate compilation step.

```javascript
/// <reference path="..." />

class Hello {
    /*:: private*/ hello(message /*: string*/) /*: Promise<{}>*/{
                const promise /*: Promise<void>*/ = null 
        return /*:: <Promise<{}>>*/ null;
    }
}
```

In the above example, we're using JSDoc comments to provide type annotations for the `hello` method. The `/*:: */` syntax is used to specify types at this level.

### _/type_ and _/tscode_ Directives

To make it easier to add type annotations without modifying our existing codebase, we can use a custom directive `_/type_[type]` to annotate variables and functions with types. We can also use `_/tscode_[tscode]` to specify the corresponding TypeScript code snippet.

```javascript
_/: [type] */_:: [tscode] */

class Hello {
    _privateVariable_: string = 'hello';
    _helloFunction_: () => void;

    constructor() {
        this._helloFunction_ = this.hello.bind(this);
    }

    hello(message: string): Promise<void> {
        const promise: Promise<void> = null;
        return promise;
    }
}
```

In the above example, we're using `_/type_[type]` and `_/tscode_[tscode]` directives to annotate variables and functions with types.

## Conclusion

By utilizing TypeScript's ability to parse types from JSDoc comments and adding custom directives for type annotations, we can enable type checking on existing JavaScript projects without requiring a separate compilation step. This allows us to benefit from features like code completion, on-the-fly error checking, and language server awesomeness directly in our browser or Node.js environment.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/9694)