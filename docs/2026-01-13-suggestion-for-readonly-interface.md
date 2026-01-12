---
title: "Simplifying Immutable Interfaces in TypeScript"
tags:
  - readonly interfaces
  - type scripting
---

# Simplifying Immutable Interfaces in TypeScript

## Core Problem

In TypeScript, when creating an interface for an immutable object, it's common to repeat the `readonly` keyword for each property. This can lead to a lot of boilerplate code and make your interface definitions harder to maintain.

## Solution & Analysis

To avoid duplicating `readonly` for each property, you can apply it directly to the interface definition itself. Here's an example:

```typescript
interface State {
  readonly prop1: string;
  readonly prop2: string;
  // ...
  readonly prop22: string;
}
```

However, this approach has a drawback - if you need to make any changes to your interface, you'll have to update the `readonly` keyword for all properties.

A better solution is to use the `Readonly<T>` type and extend it with an empty object. Here's how:

```typescript
interface State extends Readonly<{ prop1: string; prop2: string; // ... prop22: string; }> { }
```

This approach achieves the same result without duplicating `readonly` for each property, while also providing a clean and maintainable way to define immutable interfaces.

### Example Use Case

```typescript
interface Foo extends Readonly<{ prop1: string; prop2: number; }> { }

declare const f: Foo;

f.prop1 = 'test';
// Error: Property 'prop1' is readonly.
```

## Conclusion

By applying the `Readonly` type to your interface definition and extending it with an empty object, you can simplify the process of creating immutable interfaces in TypeScript. This approach reduces boilerplate code and makes your interface definitions easier to maintain.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/21152)