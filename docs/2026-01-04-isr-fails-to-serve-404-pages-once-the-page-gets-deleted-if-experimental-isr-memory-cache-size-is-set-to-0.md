---
title: "ISR Fails to Serve 404 Pages After Page Deletion with Experimental ISR Memory Cache Size Set to 0"
tags:
  - next.js
  - isr cache
  - memory caching
---

# ISR Fails to Serve 404 Pages After Page Deletion with Experimental ISR Memory Cache Size Set to 0

## Core Problem
When using the experimental ISR (Incremental Static Regeneration) memory cache with a size of 0, Next.js fails to serve 404 pages after page deletion. This issue arises when the ISR memory cache is disabled, causing the server to return stale versions of pages instead of the expected 404 page.

## Solution & Analysis
To resolve this issue, we need to understand how the experimental ISR memory cache works and its impact on serving 404 pages. The `isrMemoryCacheSize` option controls the amount of memory allocated for caching generated documents. When set to 0, the cache is disabled, and the server relies solely on revalidation to serve pages.

To reproduce the issue, follow these steps:

1. Build and start the production build of your Next.js application.
2. Navigate to `http://localhost:3000/detail/1` in your browser.
3. In the `public/detail.json` file, change the `enabled` parameter to 0.
4. After 5 seconds (revalidation period), refresh the page twice:
	* The first refresh should serve you the stale page while revalidating the page on server.
	* The second refresh should return a 404 page, but it does not.
	* Any later request will still serve the original stale version of the page.

To work around this issue, set `notFound: false` in your `getStaticProps` function. This tells Next.js to always return a 404 page instead of serving the stale version.

```jsx
import { GetStaticProps } from 'next';

const DetailPage = () => {
  // ...
};

export const getStaticProps: GetStaticProps = async () => {
  return {
    props: {
      notFound: false,
    },
  };
};
```

## Conclusion
By understanding the behavior of the experimental ISR memory cache and setting `notFound` to `false`, you can work around the issue of Next.js failing to serve 404 pages after page deletion.

## Reference
- [Source](https://github.com/vercel/next.js/issues/37945)