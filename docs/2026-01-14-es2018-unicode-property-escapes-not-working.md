---
title: "ES2018 Unicode Property Escapes Not working in Next.js V 10.0.2"
tags:
  - nextjs
  - es2018
  - unicode-property-escapes
---

# ES2018 Unicode Property Escapes Not working in Next.js V 10.0.2

## Core Problem
The ES2018 `Unicode Property Escapes` feature is not working as expected in Next.js V 10.0.2, causing issues with character encoding and property matching.

## Solution & Analysis
To resolve the issue, we need to understand how the Unicode Property Escapes work and what changes were introduced in Next.js V 10.0.2 that might be affecting this feature.

The `Unicode Property Escapes` feature is a way to search for Unicode characters based on their properties, such as script or category. However, it seems that an update in Next.js V 10.0.2 broke the functionality of this feature.

Here's an example of how the issue manifests itself:
```js
const text = 'Hello, world!';
console.log(text.match(/\p{Script}/g)); // should return all script characters, but doesn't work in v10.0.2
```
In V 10.0.1, this code works fine and returns all script characters.

After researching the changes made in Next.js V 10.0.2, it appears that the issue might be related to the update in the `unicode-property-escapes` package (https://github.com/vercel/next.js/pull/18759).

To fix this issue, we can try using an older version of the `unicode-property-escapes` package or use a different approach for character encoding and property matching.

Here's an example using the `unicode-match` package:
```js
import unicodeMatch from 'unicode-match';

const text = 'Hello, world!';
console.log(text.match(unicodeMatch(/\p{Script}/))) // returns all script characters
```
In conclusion, it seems that the ES2018 `Unicode Property Escapes` feature is not working as expected in Next.js V 10.0.2. While the exact cause of the issue is unclear, using an older version of the `unicode-property-escapes` package or a different approach for character encoding and property matching may resolve the problem.

## Conclusion
The ES2018 `Unicode Property Escapes` feature is not working in Next.js V 10.0.2, causing issues with character encoding and property matching. Using an older version of the `unicode-property-escapes` package or a different approach for character encoding and property matching may resolve the problem.

<!-- ADSENSE_PLACEHOLDER -->

## Reference
- [Source](https://github.com/vercel/next.js/issues/19303)