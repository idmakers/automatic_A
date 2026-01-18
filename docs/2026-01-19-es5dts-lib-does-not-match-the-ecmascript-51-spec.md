---
title: "The Misalignment of es5.d.ts and ECMAScript 5.1"
tags:
  - TypeScript
  - ECMAScript
  - es5.d.ts
---

# The Misalignment of es5.d.ts and ECMAScript 5.1

## Core Problem
As a TypeScript user, you expect that an environment configured with `"lib": ["es5"]` should only resolve types specified in the [ECMAScript 5.1 Language Specifications](https://www.ecma-international.org/ecma-262/5.1/). However, including only the "es5" lib brings in non-ES5 types and their support types.

## Solution & Analysis
To align es5.d.ts with ECMAScript 5.1, we can use one of three approaches:

### 1. Create a Middleman Library
Create a middleman library that imports es5.d.ts and includes additional "support" types. This would allow users to customize their environment without relying on the default es5.d.ts.

```ts
// lib.core.d.ts
import es5 from './es5.d.ts';

// Add support for Promise and Typed Arrays
declare global {
  interface Window {
    Promise: typeof es2015.promise.Promise;
    ArrayBuffer: typeof Uint8Array;
    TypedArray: typeof Int8Array;
  }
}
```

### 2. Specify Necessary Features Directly in the Library Source Map
Specify features that DOM APIs require directly in the library source map. This approach would allow users to customize their environment without relying on the default es5.d.ts.

```ts
// gulpfile.ts
const hostsLibrarySources = [
  'lib.dom.d.ts',
];

const libDTSources = [
  'header.d.ts',
  'es5.d.ts',
].concat(hostsLibrarySources, ['es2015.promise.d.ts']);

module.exports = {
  // ...
};
```

### 3. Declare Necessary Interfaces Inline
Declare required interfaces inline in the libraries that require them.

```ts
// node/index.d.ts
interface MapConstructor {}
interface WeakMapConstructor {}
interface SetConstructor {}
interface WeakSetConstructor {}

declare global {
  interface Window {
    Promise: typeof es2015.promise.Promise;
  }
}
```

## Conclusion
Instituting a change like this would clean up the ECMAScript type declaration file(s) and **explicitly** document why those types were included with certain libraries. This approach would decouple DOM support from ECMAScript support, enabling users to more explicitly configure their environments without surprising side-effects.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/16132)