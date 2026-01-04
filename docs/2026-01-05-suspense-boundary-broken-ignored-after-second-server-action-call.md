---

title: "Suspense Boundary Broken: A Hidden Pitfall in Next.js"
tags:
  - nextjs
  - suspense
  - performance

---

# Suspense Boundary Broken: A Hidden Pitfall in Next.js

## Core Problem

When building performant and responsive user interfaces, it's essential to understand the intricacies of Next.js' suspense mechanism. The suspense boundary is a critical aspect that ensures only the necessary components are reloaded when an action is triggered. However, there's a hidden pitfall that can lead to unexpected behavior.

After the second server action call, the suspense boundary becomes ignored, causing all server components to be returned at once after the last server component has finished rendering. This can result in poor user experience due to long-running API calls blocking the entire page update.

## Solution & Analysis

The issue can be reproduced using the example code provided on GitHub: https://github.com/trieb-work/nextjs-broken-suspense-bug-example.

```bash
// page.tsx
import { Suspense, useEffect } from 'react';
import { getSlowServerComponent } from '../api';

const Page = () => {
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    if (loading) {
      setLoading(false);
    }
  }, []);

  return (
    <div>
      {loading ? (
        <p>Page is loading...</p>
      ) : (
        <>
          <Suspense fallback={<p>Slept for 1000ms. Random digit X</p>}>
            <SlowServerComponent />
          </Suspense>

          <button onClick={() => getSlowServerComponent()}>Run server action</button>

          <Suspense fallback={<p>Slept for 3000ms. Random digit X</p>}>
            <SlowServerComponent />
          </Suspense>
        </>
      )}
    </div>
  );
};

export default Page;
```

```bash
// SlowServerComponent.tsx
import { useState, useEffect } from 'react';
import { getSlowServerComponent } from '../api';

const SlowServerComponent = () => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    if (loading) {
      setLoading(false);
    }

    return async () => {
      await new Promise(resolve => setTimeout(resolve, 3000));
      setData('Random digit X');
    };
  }, []);

  return (
    <p>
      Slept for {data ? data.length : '3000ms'}.
    </p>
  );
};

export default SlowServerComponent;
```

```bash
// api.ts
import { getSlowServerComponent } from './SlowServerComponent';

const getSlowServerComponent = async () => {
  return getSlowServerComponent();
};
```

To fix this issue, you need to re-add the suspense boundary for each action. This can be achieved by wrapping each server component in a new `Suspense` component.

```bash
// page.tsx (updated)
import { Suspense } from 'react';
import { getSlowServerComponent } from '../api';

const Page = () => {
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    if (loading) {
      setLoading(false);
    }
  }, []);

  return (
    <div>
      {loading ? (
        <p>Page is loading...</p>
      ) : (
        <>
          <Suspense fallback={<p>Slept for 1000ms. Random digit X</p>}>
            <SlowServerComponent />
          </Suspense>

          <button onClick={() => getSlowServerComponent()}>Run server action</button>

          <Suspense fallback={<p>Slept for 3000ms. Random digit X</p>}>
            <SlowServerComponent />
          </Suspense>
        </>
      )}
    </div>
  );
};

export default Page;
```

## Conclusion

The suspense boundary is a crucial aspect of Next.js that ensures only necessary components are reloaded when an action is triggered. However, if not implemented correctly, the suspense boundary can become ignored, leading to poor user experience due to long-running API calls blocking the entire page update.

By understanding and properly implementing the suspense mechanism, you can build performant and responsive user interfaces that provide a seamless user experience.

## Reference
- [Source](https://github.com/vercel/next.js/issues/66431)