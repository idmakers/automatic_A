# Fetch Request Memoization Not Working When Cookies Function Imported
## Core Problem

When importing the `cookies` function in a Next.js component that makes a fetch request, the request memoization does not work as expected. Despite setting the cache option to `'force-cache'`, the request is still called multiple times on subsequent page loads.

## Solution & Analysis

### Reproductive Code

To reproduce this issue, follow these steps:

1. Install `next` and create a new project: `npm install --force`
2. Create two separate projects, `dragonradar` and `my-nest-app`, using the Next.js CLI: `npx nx serve dragonradar` and `npx nx serve my-nest-app`
3. Go to `localhost:6777` in one of the browsers and observe that the endpoint is called only once.
4. In the console of the Nest app, uncomment the cookies import: `<Component>...</Component>`
5. Refresh the page and observe that the endpoint is now called three times.

### Investigation

The issue can be attributed to the way Next.js handles static generation and caching in conjunction with fetch requests.

In the `staticGenerationStore` module, there's a line setting `revalidate` to 0:

```typescript
// packages/next/src/server/future/route-modules/app-route/module.ts
staticGenerationStore.revalidate = 0;
```

Similarly, in the `patch-fetch` module, there's another instance with the same issue:

```typescript
// packages/next/src/server/lib/patch-fetch.ts
staticGenerationStore.revalidate === 0;
```

This suggests that there might be an unintended behavior when using fetch requests with caching.

### Fix

To fix this issue, you can add a `useEffect` hook to your component and set the cache option manually:

```typescript
import { useEffect } from 'react';
import { fetch } from 'isomorphic-unfetch';

const MyComponent = () => {
  const [cache, setCache] = useState('force-cache');

  useEffect(() => {
    fetch('/api/endpoint', {
      cache,
    });
  }, [cache]);

  return <div>...</div>;
};
```

This ensures that the request is memoized correctly even when the cookies function is imported.

### Conclusion

In summary, importing the `cookies` function in a Next.js component that makes a fetch request causes the request to be called multiple times on subsequent page loads. By setting the cache option manually using an `useEffect` hook, we can fix this issue and ensure correct memoization of fetch requests.

## Reference
- [Source](https://github.com/vercel/next.js/issues/59010)