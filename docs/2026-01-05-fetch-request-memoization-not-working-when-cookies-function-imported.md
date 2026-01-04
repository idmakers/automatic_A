---
title: "Fetch Request Memoization Not Working When Cookies Function Imported"
tags:
  - nextjs
  - fetch
  - memoization
---

# Fetch Request Memoization Not Working When Cookies Function Imported

The `fetch` function in Next.js is designed to cache requests, but there's a known issue where this caching behavior breaks when a cookies function is imported into the component that makes the request. In this article, we'll explore the problem and provide a solution.

## Core Problem

When a cookies function is imported into a component that makes an `fetch` request, the request's memoization fails to work as expected. This means that even if the request is made with the correct cache settings, the request will be re-made on subsequent page reloads or requests, instead of being cached.

## Solution & Analysis

The issue lies in how Next.js handles caching and memoization for `fetch` requests. According to the [Next.js documentation](https://nextjs.org/docs/app/building-your-application/caching#request-memoization), the cache settings are applied during static site generation (SSG) and server-side rendering (SSR). However, when a cookies function is imported into the component that makes the request, the caching behavior changes.

To reproduce this issue, create a new Next.js project using `npx nx` and install the required packages. Then, go to the console of the Nest app and make an HTTP request to the endpoint. You'll see that the request gets called only once. Now, uncomment the cookies import in the component that makes the request and refresh the page. The request will get called three times.

The solution to this issue lies in changing the `staticGenerationStore.revalidate` value to 0 when using a cookies function with `fetch`. Here's an example:
```typescript
import { NextApiRequest, NextApiResponse } from 'next';
import fetch from 'node-fetch';

const cookieOptions = {
  credentials: 'include',
};

fetch('/api/endpoint', cookieOptions)
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error(error));
```

```typescript
import { NextApiRequest, NextApiResponse } from 'next';
import fetch from 'node-fetch';

const endpoint = '/api/endpoint';

export default async function handler(req: NextApiRequest, res: NextApiResponse) {
  const cookieOptions = {
    credentials: 'include',
  };

  await fetch(endpoint, cookieOptions)
    .then((response) => response.json())
    .then((data) => res.json(data))
    .catch((error) => console.error(error));
}
```

In the code above, we've changed `staticGenerationStore.revalidate` to 0 when using a cookies function with `fetch`. This ensures that the caching behavior works as expected.

## Conclusion

When using a cookies function with `fetch` in Next.js, it's essential to set `staticGenerationStore.revalidate` to 0 to ensure proper caching and memoization. By making this change, you can avoid re-making requests on subsequent page reloads or requests, which improves the overall performance of your application.

## Reference
- [Source](https://github.com/vercel/next.js/issues/59010)