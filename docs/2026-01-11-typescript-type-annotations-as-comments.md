---
title: "TypeScript Type Annotations as Comments: Revolutionizing Code Reviews and Development"
tags:
  - TypeScript
  - Type Checking
  - JavaScript
---

# TypeScript Type Annotations as Comments: Revolutionizing Code Reviews and Development

## Core Problem
typescript’s goal is simply be Javascript + Types, but there are many use cases where one might want to use the excellent typechecker but not really have any emit stage. Projects already written in javascript work with allowJS, TypeScript already supports parsing types from jsdoc comments.

## Solution & Analysis
### Top Solution/Comment

Flow has a descent spec for annotating code with type information, as seen in [https://flowtype.org/blog/2015/02/20/Flow-Comments.html](https://flowtype.org/blog/2015/02/20/Flow-Comments.html).

```javascript
/// <reference path="..." />

class Hello {
    /*:: private*/ hello(message /*: string*/) /*: Promise<{}>*/{
                const promise /*: Promise<void>*/ = null 
        return /*:: <Promise<{}>>*/ null;
    }
}
```

The TypeScript language service is a major value add. Being able to annotate an existing js code base and get on the fly error checking + code completion is pure awesomeness.

### Proposed Solution

 `_: [type]`
_::_:: [tscode]_

This syntax allows developers to add type annotations directly to their JavaScript code, without needing to write a separate types file. This approach eliminates the need for source mapping and reduces compilation time.

### Benefits
* No source mapping needed: The browser reads the same code, eliminating the need for source maps.
* Browser/node reads the same code: Development becomes faster as we can directly read and understand our code.
* No waiting for a transpiling step: We get immediate type checking and code completion.
* No separate typings install needed: Node_modules written in this flavor don't require a separate typings install since source+types is the same file.

## Conclusion
TypeScript type annotations as comments are a game-changer for existing JavaScript projects. By annotating our code with types, we can unlock the full potential of TypeScript's language service, making development faster and more enjoyable.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/9694)