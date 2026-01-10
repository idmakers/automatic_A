---
title: "Simplifying Immutable Interfaces in TypeScript"
tags:
  - readonly interfaces
  - immutable objects
---

# Simplifying Immutable Interfaces in TypeScript

## Core Problem
When defining read-only interfaces in TypeScript, developers often find themselves duplicating the `readonly` keyword for each property. This can lead to unnecessary boilerplate code and make maintenance more challenging.

## Solution & Analysis
To avoid this issue, you can apply the `readonly` keyword directly to the interface definition itself.

### Existing Issue

```ts
interface State {
    readonly prop1: string;
    readonly prop2: string;
    ...
    readonly prop22: string;
}
```

### Simplified Solution

This can already be achieved with a little bit of boilerplate:

```typescript
interface State extends Readonly<{ prop1: string; prop2: string; ... prop22: string; }> { }
```

By using the `Readonly` type and extending it from an object literal, you eliminate the need to repeat the `readonly` keyword for each property.

## Conclusion

By applying the `readonly` keyword directly to the interface definition, developers can simplify their code and reduce boilerplate. This approach also makes it easier to maintain consistency across large-scale applications.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/21152)