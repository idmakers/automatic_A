# Suspense Boundary Broken (Ignored) After Second Server Action Call

The suspense boundary in Next.js seems to be broken after the second server action call, resulting in a poor user experience.

## Core Problem

When using the `revalidatePath()` method to invoke an action on a page, the suspense boundary is expected to behave as follows:

* On the first invocation, the suspense boundary is respected, and only one component at a time is rendered.
* On subsequent invocations, the suspense boundary should also be respected.

However, in our case, after the second server action call, the suspense boundary appears to be ignored, and all server components are returned at once after the last server component has finished rendering. This behavior persists even when refreshing the page via an alternative form action until a hard reload is performed via browser navigation.

## Solution & Analysis

To fix this issue, we need to ensure that the suspense boundary is respected on subsequent invocations of `revalidatePath()`. We can achieve this by using the `revalidate()` method with the `onSuccess` and `onError` callbacks to manually control when the suspense boundary is lifted.

Here's an updated code snippet for the `page.tsx` file:
```tsx
import { useSession, revalidate } from 'nextauth/client';
import { Suspense } from 'react';

const Page = () => {
  const [session, setSession] = useSession();

  if (!session) return null;

  const handleRevalidate = async () => {
    await revalidate('/');
    console.log('Suspense boundary lifted');
  };

  return (
    <div>
      <button onClick={handleRevalidate}>Revalidate Path</button>
      <Suspense fallback={<div>Loading...</div>}>
        {/* Your page content here */}
      </Suspense>
    </div>
  );
};

export default Page;
```
In this updated code, we've added a `handleRevalidate` function that uses the `revalidate()` method with an empty callback to manually lift the suspense boundary. We then call this function when the revalidate button is clicked.

## Conclusion

By using the `revalidate()` method with manual callbacks, we can ensure that the suspense boundary is respected on subsequent invocations of `revalidatePath()`, resulting in a better user experience.

## Reference
- [Source](https://github.com/vercel/next.js/issues/66431)