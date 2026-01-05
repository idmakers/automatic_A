**Dynamic Routes in App Router are Considered Server Functions**

The App Router in Next.js has introduced a new feature that can cause confusion regarding the behavior of dynamic routes. In this blog post, we will explore why dynamic routes in App Router are considered server functions and how to work around this issue.

**The Problem**

In the latest Next.js canary release, the App Router is treating `/test/[slug]` as a server route despite its use of no dynamic functions. This means that every time the route is accessed, it triggers a function execution on the server, which defeats the purpose of using dynamic routes in the first place.

**Expected Behavior**

We expect the route `/test/[slug]` to be a static route and be cached (ISR/Full Route Cache), but instead, it's being treated as a server route. This behavior is not only inefficient but also affects the performance of our application.

**Solution & Analysis**

The reason for this behavior lies in the way Next.js handles dynamic routes. By default, App Router assumes that you want to generate the page at runtime. However, if you want to generate it at build time, you need to use `generateStaticParams`.

To fix this issue, we can set the following flags:

```ts
export const dynamic = "error";
export const dynamicParams = true;
```

By doing so, Next.js will consider the route as a static route and cache it, which is our desired behavior.

Here's an example of how you can modify your `page.tsx` file to use `generateStaticParams`:

```tsx
import { useRouter } from 'next/router';

const Test = ({ params: { slug } }: { params: { slug: string } }) => {
  return <div>Slug: {slug}</div>;
};

export const generateStaticParams = () => [
  // Generate the static routes for each unique slug
  ['/test/', '/test/:slug'],
];

export default Test;
```

In this example, we're generating a static route for both `/test/` and `/test/[slug]`. This tells Next.js to cache these pages at build time.

**Conclusion**

The App Router in Next.js can be finicky when it comes to dynamic routes. By understanding how it handles server functions and using `generateStaticParams`, we can work around this issue and ensure that our application performs efficiently.

Please let me know if you want any further assistance with this blog post.

## Reference
- [Source](https://github.com/vercel/next.js/issues/53721)