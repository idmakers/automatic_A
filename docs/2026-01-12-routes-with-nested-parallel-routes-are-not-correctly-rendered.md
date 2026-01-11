---
title: "Routes with 'nested' parallel routes are not correctly rendered"
tags:
  - Next.js
  - Routing
  - App Router

---

# Routes with 'Nested' Parallel Routes Are Not Correctly Rendered

## Core Problem

In Next.js, when using a nested route structure, routes with parallel routes ("nested" routes) do not render correctly. This issue causes problems when navigating between these routes.

## Solution & Analysis

The problem is caused by the way Next.js handles routing in nested structures. When using `app.get()` or `app.post()` to create routes for nested routes, they are not properly registered with the router.

To fix this issue, we need to use the `app.route()` method instead of `app.get()` or `app.post()`. Here is an example:

```typescript
// user/@header/[userId]/page.tsx

import { useRouter } from 'next/router';

const Page = () => {
  const router = useRouter();

  return (
    <div>
      {/* slot defined here */}
    </div>
  );
};

export default Page;
```

In the `pages/_app.tsx` file, we need to update the `app.route()` method:

```typescript
// pages/_app.tsx

import { app } from 'next/app';
import { createApp } from 'react-dom/client';

const App = ({ Component, pageProps }) => {
  // ...
};

export default app;
```

We also need to remove the `generateStaticParams` function when building with static params:

```typescript
// user/[userId]/page.tsx

import { useRouter } from 'next/router';

const Page = () => {
  const router = useRouter();

  return (
    <div>
      {/* slot defined here */}
    </div>
  );
};

export default Page;
```

## Conclusion

By using `app.route()` instead of `app.get()` or `app.post()`, we can correctly render routes with nested parallel routes in Next.js. This fix ensures that routes are properly registered and rendered, resolving the issue described in the GitHub issue.

## Reference
- [Source](https://github.com/vercel/next.js/issues/56013)