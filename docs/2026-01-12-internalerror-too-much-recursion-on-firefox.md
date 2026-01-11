---
title: "InternalError: too much recursion on Firefox"
tags:
  - next.js
  - firefox
  - internalerror
---

# InternalError: too much recursion on Firefox

## Core Problem
A common issue reported by users is an `InternalError: too much recursion` error that occurs when using the App Router in Next.js with Firefox.

## Solution & Analysis
The problem lies in a bug introduced in Next.js 13.5.6-canary.1, which affects how the App Router handles recursive routes. To reproduce this issue, follow these steps:

### Step-by-Step Reproduction

1. Install dependencies using `npm install` or `yarn install`.
2. Run the `dev` task using `npm run dev` or `yarn dev`.
3. Open http://localhost:3000 in Firefox (version 118.0.2).

To fix this issue, you can try updating to the latest stable version of Next.js (`13.5.6`) instead of using the Canary release.

### Code Example

Here's an example of how to update your `next.config.js` file:
```js
module.exports = {
  // ... other configurations ...
  experimental: {
    appDir: true,
  },
};
```
Alternatively, you can use the `--experimental-appDir` flag when running the `dev` task:

```bash
npm run dev --experimental-appDir
```

### Conclusion
By updating to the latest stable version of Next.js or using the `--experimental-appDir` flag, you should be able to resolve the `InternalError: too much recursion` issue on Firefox.

## Reference
- [Source](https://github.com/vercel/next.js/issues/56921)