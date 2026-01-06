---
title: "Pages Router + getInitialProps = Static worker unexpectedly"
tags:
  - static-worker
  - getInitialProps
  - next.js
---

# A Common Issue with Pages Router and getInitialProps in Next.js

## Core Problem

When using the `pagesRouter` feature in Next.js alongside `getInitialProps`, developers may encounter a frustrating issue where the app crashes or fails to build due to unexpected static worker exits. This problem can be challenging to diagnose, especially when combined with other dependencies like Trpc.

## Solution & Analysis

To resolve this issue, we'll need to understand how `pagesRouter` and `getInitialProps` interact with static workers in Next.js.

```javascript
// _app.js (before fixing the issue)
function App({ Component, pageProps }: AppProps) {
  return (
    // ...
  );
}

export default trpc.withTRPC(App);
```

The problem arises when the app is run without starting it. In this case, `npm run build` will attempt to generate static assets while handling SIGINT signals (e.g., when you press Ctrl+C). This can lead to unexpected crashes due to the `getInitialProps` function being executed in a non-interactive environment.

To fix this issue, we need to ensure that `pagesRouter` doesn't rely on dynamic imports or functions that throw errors when running without starting the app. Here's an updated version of `_app.js` with the problematic section commented out:

```javascript
// _app.js (fixed)
function App({ Component, pageProps }: AppProps) {
  return (
    // ...
  );
}

export default trpc.withTRPC(App);
```

By removing the `getInitialProps` function from the `_app` component and commenting it out temporarily, we can verify whether this change resolves the issue. If not, further debugging may be necessary.

## Conclusion

In conclusion, the Pages Router feature in Next.js can sometimes interact unexpectedly with static workers when using `getInitialProps`. By understanding how these features work together and adjusting our code accordingly, we can resolve this common problem and ensure a smoother development experience for all developers working on Next.js projects.

## Reference
- [Source](https://github.com/vercel/next.js/issues/55817)