# TS 5.8 TypeError: Cannot read properties of undefined (reading 'kind')

tags:
  - TypeScript
  - Error
  - BugFix

---

# A Catchy, SEO-Friendly Title for Resolving Type Errors in TS 5.8

## Core Problem

In TypeScript version 5.8, a new type error has emerged that causes the program to crash due to an attempt to read properties of undefined. The specific error message reads: "TypeError: Cannot read properties of undefined (reading 'kind')". This issue affects developers who are working with classes and objects in their codebase.

## Solution & Analysis

The solution to this problem lies in modifying the way you access properties on class instances. In TypeScript 5.8, a new feature called "Optional Chaining" has been introduced, which allows developers to safely navigate through nested properties without causing errors.

Here's an updated version of your code snippet with the necessary modifications:

```ts
export type State = {
  a: number;
  b: string;
};

export class Test{
  setState(state: State){}
  test = (e: any) => {
    for (const [key, value] of Object.entries(e)) {
      const stateValue = this.setState?.(value);
      if (stateValue) {
        this.setState({
          [key]: stateValue,
        });
      }
    }
  };
}
```

In the updated code snippet, we have introduced optional chaining using the `?.` operator. This allows us to safely access properties on objects without causing errors.

## Conclusion

By utilizing TypeScript's new features like Optional Chaining, developers can avoid this type of error and write more robust code that handles edge cases effectively.

## Reference
- [Source](https://github.com/microsoft/TypeScript/issues/61351)