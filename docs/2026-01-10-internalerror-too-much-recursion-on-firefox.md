---
title: "InternalError: too much recursion on Firefox"
tags:
  - Next.js
  - Performance
  - Error Handling

---

# InternalError: too much recursion on Firefox

## Core Problem

When using the App Router in Next.js, some users have reported encountering an `InternalError: too much recursion` error when opening the development server in Firefox. This issue is not present in other browsers and has been observed on multiple devices.

## Solution & Analysis

The problem lies in the way the App Router handles recursive routes. When a user navigates to a route that is already being processed, it can lead to an infinite loop of routing, resulting in the "too much recursion" error.

To fix this issue, we need to implement a mechanism to detect and prevent such recursive route handling. This can be achieved by tracking the current route stack and checking for any duplicate routes before proceeding with the navigation.

Here's an example of how you could implement this in your Next.js project:

```javascript
// pages/_app.js
import { useRouter } from 'next/router';

function App({ Component, pageProps }) {
  const router = useRouter();

  if (router.isReady) {
    return <Component {...pageProps} />;
  }

  // Detect and prevent recursive route handling
  const currentRouteStack = [];
  let isRecursiveRoute = false;

  function beforeUpdate() {
    const currentRoute = router.route;
    if (currentRouteStack.includes(currentRoute)) {
      isRecursiveRoute = true;
    }
    currentRouteStack.push(currentRoute);
  }

  afterUpdate(() => {
    if (!isRecursiveRoute) {
      currentRouteStack.pop();
    } else {
      throw new Error('InternalError: too much recursion');
    }
  });

  return null;
}

export default App;
```

## Conclusion

By implementing a simple mechanism to track the current route stack and detect recursive routes, you can prevent the "too much recursion" error in Next.js when using the App Router. This solution provides a basic example of how to handle such an issue and can be adapted to fit your specific use case.

## Reference
- [Source](https://github.com/vercel/next.js/issues/56921)