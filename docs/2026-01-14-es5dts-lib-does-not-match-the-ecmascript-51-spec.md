---
title: "Why TypeScript's es5.d.ts library Doesn't Match ECMAScript 5.1"
tags:
  - TypeScript
  - es5
  - ECMAScript
---

# Why TypeScript's es5.d.ts Library Doesn't Match ECMAScript 5.1

## Core Problem
The es5.d.ts library in TypeScript does not match the ECMAScript 5.1 standard, causing confusion and potential bugs in properly initialized environments.

## Solution & Analysis

### Creating a Middleman Library

One possible solution is to create a middleman library that imports es5.d.ts and additional "support" types. This could be done by creating a `core.d.ts` file that imports es5.d.ts and the necessary support types, then specifying this file in the `lib.d.ts` library source map.

```ts
// core.d.ts
import * as es5 from './es5.d.ts';
import { Promise } from 'es2015.promise.d.ts';

declare namespace Core {
  interface MapConstructor {}
  interface WeakMapConstructor {}
  interface SetConstructor {}
  interface WeakSetConstructor {}
}

// lib.d.ts
{ target: "lib.dom.d.ts", sources: ["header.d.ts", "dom.generated.d.ts"].concat("es2015.promise.d.ts") }
{ target: "lib.d.ts", sources: ["header.d.ts", "core.d.ts"].concat(hostsLibrarySources, "es2015.promise.d.ts") }
```

### Specifying Necessary Features Directly in the Library Source Map

Another possible solution is to specify necessary features directly in the library source map. For example, for promises, this could look like:

```ts
// lib.dom.d.ts
{ target: "lib.dom.d.ts", sources: ["header.d.ts", "dom.generated.d.ts"].concat("es2015.promise.d.ts") }
```

### Declaring Necessary Interfaces Inline

A third possible solution is to declare necessary interfaces inline in the libraries that require them. For example:

```ts
// node/index.d.ts
interface MapConstructor { }
interface WeakMapConstructor { }
interface SetConstructor { }
interface WeakSetConstructor { }
```

## Conclusion
Instituting a change like this would not only clean up the ECMAScript type declaration file(s), but also **explicitly** document why those types were included with certain libraries. This would decouple DOM support from ECMAScript support, enabling users to more explicitly configure their environments without surprising side-effects.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/16132)