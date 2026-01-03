# Fetch Request Memoization Not Working When Cookies Function Imported

## Core Problem

When importing the `cookies` function in a Next.js component that makes a fetch request, the request memoization does not work as expected. This issue is reproducible and affects both App Router and Data fetching (gS(S)P, getInitialProps).

## Solution & Analysis

The problem lies in the fact that when the `cookies` function is imported, it alters the behavior of the `fetch` function. To understand this, let's take a look at the code.

```javascript
// packages/next/src/server/lib/patch-fetch.ts
staticGenerationStore.revalidate = 0;
```

As we can see, the `revalidate` property is set to 0 when the `cookies` function is imported. This has an impact on how the `fetch` function behaves, especially in static generation scenarios.

To fix this issue, you need to remove the import of the `cookies` function from your component that makes the fetch request. If you don't want to do that, you can try setting the `cache` option to `'force-cache'` when calling `fetch`.

```javascript
// user.tsx
import { fetch } from 'isomorphic-unfetch';

function User() {
  return (
    <div>
      <h1>User</h1>
      <p>Hello World!</p>
      <button onClick={() => fetch('/api/user', { cache: 'force-cache' })}>
        Fetch user data
      </button>
    </div>
  );
}
```

## Conclusion

In conclusion, when importing the `cookies` function in a Next.js component that makes a fetch request, it alters the behavior of the `fetch` function. To fix this issue, you need to remove the import or set the `cache` option to `'force-cache'`. This ensures that the request memoization works as expected.

**Note:** The above solution and analysis are based on the provided code and GitHub repo link. It's recommended to test and verify the solution in your local environment before applying it to your production codebase.

## Reference
- [Source](https://github.com/vercel/next.js/issues/59010)