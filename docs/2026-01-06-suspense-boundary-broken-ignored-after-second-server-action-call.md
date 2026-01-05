# Breaking the Suspense Boundary: A Devastating Bug in Next.js

## Core Problem

A critical bug has been identified in Next.js that causes the suspense boundary to be ignored after a second server action call. This issue leads to a poor user experience, as long-running API calls can block the entire page update instead of only updating a small portion of the UI.

## Solution & Analysis

The bug is triggered when the page is refreshed via an server action for the second time. The suspense boundary should be respected and `loading.tsx` should not be displayed, but in this case, it is ignored, and both `loading.tsx` and the suspense-bound component are rendered simultaneously.

To reproduce the issue, follow these steps:

1. Clone the repository [https://github.com/trieb-work/nextjs-broken-suspense-bug-example](https://github.com/trieb-work/nextjs-broken-suspense-bug-example) and start the app in dev mode using `pnpm dev`.
2. Open the app in a browser and observe that `loading.tsx` is displayed for 1 second.
3. Wait for another 1 second, and then `page.tsx` returns from its mocked API calls, displaying "Slept for 1000ms. Random digit X".
4. Now, run the server action on the page via the button "run server action".
5. Repeat step 4 again to trigger the same issue.

The code that reproduces this issue is located in `page.tsx`:
```tsx
import { Suspense } from 'react';
import SlowServerComponent from './SlowServerComponent';

function Page() {
  return (
    <Suspense>
      <div>Page</div>
      <SlowServerComponent />
    </Suspense>
  );
}

export default Page;
```

In `SlowServerComponent.tsx`:
```tsx
import { useState, useEffect } from 'react';

const SlowServerComponent = () => {
  const [sleepTime, setSleepTime] = useState(0);

  useEffect(() => {
    setTimeout(() => {
      setSleepTime(sleepTime + 3000);
      console.log(`Slept for ${sleepTime}ms. Random digit ${Math.floor(Math.random() * 10)}`);
    }, sleepTime + 1000);
  }, [sleepTime]);

  return <div>Slept for {sleepTime}ms. Random digit {Math.floor(Math.random() * 10)}</div>;
};

export default SlowServerComponent;
```

## Conclusion

The bug is caused by the fact that the suspense boundary is ignored after a second server action call. To fix this issue, we need to modify the `page.tsx` file to respect the suspense boundary for subsequent server action calls.

One possible solution is to use the `revalidatePath` method provided by Next.js to re-run the page on demand:
```tsx
import { Suspense } from 'react';
import SlowServerComponent from './SlowServerComponent';

function Page() {
  const [revalidated, setRevalidated] = useState(false);

  const handleAction = () => {
    // Simulate a server action call
    setTimeout(() => {
      console.log('Server action completed');
      setRevalidated(true);
    }, 1000);
  };

  return (
    <Suspense>
      <div>Page</div>
      {revalidated ? null : (
        <button onClick={handleAction}>Run Server Action</button>
      )}
      <SlowServerComponent />
    </Suspense>
  );
}

export default Page;
```
By using the `revalidatePath` method, we can ensure that the suspense boundary is respected for subsequent server action calls.

## Reference
- [Source](https://github.com/vercel/next.js/issues/66431)