---
title: "Adding Length Parameter to Typed Arrays"
tags:
  - typed arrays
  - type safety
---

# Adding Length Parameter to Typed Arrays

## Core Problem
Typed arrays have a fixed length, which can lead to errors when trying to assign or access an array with the wrong length.

## Solution & Analysis
### Current Limitation

```ts
type Vector2d = Int32Array<2>;
type Vector3d = Int32Array<3>;
type Matrix = Int32Array<16>;

const add =
  (vecA: Vector2d) =>
    (vecB: Vector2d, target: Vector2d = new Int32Array(2)): Vector2d => {
      target[0] = vecA[0] + vecB[0];
      target[1] = vecA[1] + vecB[1];
      return target;
    };

const add3d =
  (vecA: Vector3d) =>
    (vecB: Vector3d, target: Vector3d = new Int32Array(3)): Vector3d => {
      target[0] = vecA[0] + vecB[0];
      target[1] = vecA[1] + vecB[1];
      target[2] = vecA[2] + vecB[2];
      return target;
    };

const position = new Int32Array(2);
const position3d = new Int32Array(3);
const velocity = new Int32Array([1, 1]);
const velocity3d = new Int32Array([1, 2, 3]);

add(position)(velocity, position);
const newPosition = add(position)(velocity);

add3d(position3d)(velocity3d, position3d);
add(position3d)(velocity3d, position3d); // Fails due to incorrect array length
```

### Solution Proposal

We can extend the `Int32Array` type by adding a `length` property and modifying the constructor to accept an optional `length` parameter.

```ts
type Vector2d = MyInt32Array<2>;
type Vector3d = MyInt32Array<3>;
type Matrix = MyInt32Array<16>;

interface MyInt32Array<T extends number> extends Int32Array {
  length: T;
}

interface Int32ArrayConstructor {
  new<T extends number>(length?: T): MyInt32Array<T>;
}
```

### Implementation

```ts
class Int32Array<T extends number> {
  private _array: T[];
  public readonly length: T;

  constructor(length?: T) {
    if (length === undefined) {
      this._array = new Array(0);
    } else {
      this._array = new Array(length);
    }
    this.length = length;
  }

  get(index: number): T {
    return this._array[index];
  }

  set(index: number, value: T) {
    this._array[index] = value;
  }
}
```

## Conclusion
By adding a `length` parameter to the `Int32Array` type and implementing it correctly, we can improve type safety and prevent errors caused by incorrect array lengths.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/18471)