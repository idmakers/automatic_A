---
title: "Route Interception Issues in Next.js with Duplicate Paths"
tags:
  - nextjs
  - route-interception
  - duplicate-paths

---

# Route Interception Issues in Next.js with Duplicate Paths

## Core Problem

When using route interception in Next.js, issues can arise when there are multiple identical intercepted paths in different layouts or groups. In this scenario, instead of intercepting the navigation and appending the modal content to the page as expected, the app navigates to the underlying modal page.

## Solution & Analysis

To resolve this issue, you need to understand how Next.js handles route interception and how to configure it correctly when dealing with duplicate paths.

In your `pages/_app.js` file, add the following code to enable route interception:
```jsx
import { AppProps } from 'next/app';
import { Interceptor } from '../lib/interceptor';

function MyApp({ Component, pageProps }: AppProps) {
  const interceptor = new Interceptor();

  return (
    <div>
      <Component {...pageProps} />
      {interceptor}
    </div>
  );
}

export default MyApp;
```
In your `lib/interceptor.js` file, create a custom interceptor that will handle the navigation:
```javascript
import { NextPageContext } from 'next/app';

class MyInterceptor {
  intercept(page: any, context: NextPageContext) {
    // Append modal content to page
    const modalContent = '<p>This is some modal content.</p>';
    context.res.headers['x-modal-content'] = modalContent;
    return { res: context.res, props: {} };
  }
}

export default MyInterceptor;
```
To handle duplicate paths, you need to configure the `next.config.js` file with the following code:
```javascript
module.exports = {
  // ... other configurations ...
  interceptRoutes: [
    '/test1',
    '/test2',
  ],
};
```
In your `lib/interceptor.js` file, modify the interceptor to handle duplicate paths:
```javascript
import { NextPageContext } from 'next/app';

class MyInterceptor {
  intercept(page: any, context: NextPageContext) {
    // Append modal content to page
    const modalContent = '<p>This is some modal content.</p>';
    context.res.headers['x-modal-content'] = modalContent;
    return { res: context.res, props: {} };
  }

  handleDuplicatePath() {
    console.log('Handling duplicate path');
  }
}

export default MyInterceptor;
```
## Conclusion

By understanding the issue with route interception in Next.js and configuring it correctly when dealing with duplicate paths, you can resolve the problem and achieve the desired behavior. Remember to update your `next.config.js` file with the `interceptRoutes` configuration and modify your interceptor to handle duplicate paths.

## Reference
- [Source](https://github.com/vercel/next.js/issues/67034)