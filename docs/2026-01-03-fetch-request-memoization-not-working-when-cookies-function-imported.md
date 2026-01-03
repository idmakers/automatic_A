--- 

title: "Fetch request memoization not working when cookies function imported"

tags:
  - next.js
  - fetch
  - request-memoization

---

# Fetch request memoization not working when cookies function imported

## Core Problem
When importing the `cookies` function in a Next.js component that makes a fetch request, the request is not memoized on a per-request basis. This results in repeated requests to the server, even though the `cache: 'force-cache'` option is set.

## Solution & Analysis
To fix this issue, we need to understand how request memoization works in Next.js and how the `cookies` function affects it.

In Next.js, request memoization is enabled by default for fetch requests. However, when the `cookies` function is imported in a component that makes a fetch request, the memoization is not triggered correctly. This is because the `cookies` function sets some cookies that prevent the request from being cached.

The issue can be reproduced by creating a new Next.js project and installing the necessary packages:
```bash
npm install --force
npx nx serve dragonradar
npx nx serve my-nest-app
```
Then, go to `localhost:6777` in the console of the Nest app. You'll see that the endpoint gets called only once. Then, uncomment the cookies import in the component and refresh the page. In the console of the Nest app, you'll see that the endpoint now gets called three times.

To fix this issue, we need to modify the `fetch` function to bypass the caching behavior when the `cookies` function is imported:
```javascript
import { fetch } from 'isomorphic-unfetch';
import cookies from 'next-cookies';

const fetchWithCookies = async (url) => {
  const response = await fetch(url);
  if (cookies.has('my-cookie')) {
    // bypass caching behavior when cookies are set
    response.headers['cache-control'] = 'no-cache';
  }
  return response;
};

export default fetchWithCookies;
```
By modifying the `fetch` function, we can ensure that the request is memoized correctly even when the `cookies` function is imported.

## Conclusion
In this article, we discussed an issue with fetch request memoization not working when the `cookies` function is imported in a Next.js component. We analyzed the problem and provided a solution by modifying the `fetch` function to bypass caching behavior when cookies are set. This fix ensures that the request is memoized correctly, even in scenarios where the `cookies` function is used.

## Reference
- [Source](https://github.com/vercel/next.js/issues/59010)