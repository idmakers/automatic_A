---
title: "React 19.0.0 actQueue Infinite Growth Bug"
tags:
  - React 19.0.0
  - actQueue
  - infinite growth
---

# React 19.0.0 actQueue Infinite Growth Bug

## Core Problem
When migrating from React 18.3.1 to React 19.0.0, a unit test starts to fail due to an infinite loop in the `actQueue`. This issue is caused by the use of `<Suspense />` and `react.lazy` along with a component that has a `const [ref, setRef] = useState(null)` pattern.

## Solution & Analysis
To solve this issue, we need to identify the cause of the infinite loop. Based on the provided information, we can try the following solutions:

### Solution 1: Remove `setRef` callsite from ref props

```jsx
// Before
<div ref={(ref) => setRef(ref)} />

// After
<div />
```

By removing the `setRef` callsite from the ref props, the test can finish.

### Solution 2: Remove `Suspense` and `react.lazy`

```jsx
// Before
<Suspense fallback={<div>Loading...</div>}>
  <LazyComponent />
</Suspense>

// After
<LazyComponent />
```

By removing the `Suspense` and `react.lazy`, the test can finish.

### Solution 3: Remove usage of `ref` state from effect

```jsx
// Before
const [ref, setRef] = useState(null)
useEffect(() => {
  // code that uses ref as a dependency
}, [ref])
```

By removing the usage of `ref` state from the effect, the test still hangs.

### Additional Analysis

The `actQueue` is a mechanism used by React to batch and manage asynchronous effects. In this case, the infinite loop is caused by the use of `Suspense` and `react.lazy`, which creates an additional layer of complexity in the actQueue.

To fix this issue, we need to refactor the component tree to avoid using `Suspense` and `react.lazy`. We can also try to optimize the effect by removing unnecessary dependencies or using a different approach to manage asynchronous effects.

## Conclusion
By analyzing the problem and providing potential solutions, we can help identify the root cause of the infinite loop in React 19.0.0. By avoiding the use of `Suspense` and `react.lazy`, as well as optimizing effects, we can potentially fix the issue and improve the overall performance of the application.

## Reference
- [Source](https://github.com/facebook/react/issues/34250)