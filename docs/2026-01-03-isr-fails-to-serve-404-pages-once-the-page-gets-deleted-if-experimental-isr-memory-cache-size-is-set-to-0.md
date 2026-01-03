---
title: "ISR Fails to Serve 404 Pages after Deleting Page with Experimental ISR Memory Cache Size Set to 0"
tags:
  - Next.js
  - ISR (Incremental Static Regeneration)
  - Caching
  - Bug Fix
---

# ISR Fails to Serve 404 Pages after Deleting Page with Experimental ISR Memory Cache Size Set to 0

## Core Problem
When the experimental ISR memory cache size is set to 0, deleted ISR pages fail to serve a 404 page, instead returning the stale version of the page.

## Solution & Analysis
To fix this issue, you can use the following solutions:

### Solution 1: Set `notFound : false` in `getStaticProps`
```javascript
import { GetStaticProps } from 'next';

const Page = () => {
  // ...
};

export const getStaticProps: GetStaticProps = async ({ notFound }) => {
  if (notFound) return { notFound: true };
  // ...
};
```

### Solution 2: Use `isrMemoryCacheSize` to disable caching for deleted pages
```javascript
import { NextConfig } from 'next/config';

const config = NextConfig({
  // ...
  experimental: {
    isrMemoryCacheSize: 0,
  },
});
```
By setting `isrMemoryCacheSize` to 0, you can disable the ISR memory cache for deleted pages, ensuring that they serve a 404 page instead of returning the stale version.

## Conclusion
In summary, when using the experimental ISR memory cache size set to 0, deleted ISR pages may fail to serve a 404 page. By setting `notFound : false` in `getStaticProps` or disabling caching for deleted pages using `isrMemoryCacheSize`, you can fix this issue and ensure that your Next.js application serves correct 404 pages.