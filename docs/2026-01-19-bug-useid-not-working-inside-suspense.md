---
title: "Bug: `useId()` not working inside `<Suspense>`"
tags:
  - React Hooks
  - Suspense Component
---

# Bug: `useId()` not working inside `<Suspense>`

## Core Problem
When using the `useId` hook inside a `<React.Suspense>` component, it fails to return a stable ID. This issue is crucial for various React tools and libraries that rely on the uniqueness of IDs.

## Solution & Analysis

The problem lies in how React handles component mounting during initial render when using `<Suspense>`. When the component is suspended during the initial render, it is not mounted, which means it cannot store any associated state. This includes the state used by `useId` to generate a unique ID.

To demonstrate this issue, consider the following code snippet:

```jsx
function App() {
  return (
    <React.Suspense fallback={<p>I'm lazy loaded...</p>}>
      <LazyComponent />
    </React.Suspense>
  )
}

function LazyComponent() {
  const id = useId()
  console.log('id:', id)

  // The usual `throw promise` technique
  // ...
}
```

In this example, the `useId` hook is called inside a `<React.Suspense>` component. When the component is suspended during initial render, it cannot store any associated state, including the state used by `useId`. As a result, the ID returned by `useId` changes with each render.

To fix this issue, you can use the `createRef` hook to maintain the state of the `useId` hook outside of the suspended component. Here's an updated code snippet:

```jsx
function App() {
  const idRef = React.createRef();

  return (
    <React.Suspense fallback={<p>I'm lazy loaded...</p>}>
      <LazyComponent ref={idRef} />
    </React.Suspense>
  )
}

function LazyComponent({ ref }) {
  const id = useId()

  if (ref.current) {
    console.log('id:', ref.current)
    // ...
  }

  return null
}
```

In this updated code snippet, the `createRef` hook is used to create a reference (`idRef`) that maintains the state of the `useId` hook. The `LazyComponent` component receives this reference as a prop and uses it to log the ID when rendered.

## Conclusion

The bug with `useId()` not working inside `<Suspense>` is due to React's behavior during initial render when using `<Suspense>`. To fix this issue, you can use the `createRef` hook to maintain the state of the `useId` hook outside of the suspended component. This solution ensures that the ID returned by `useId` remains stable even when the component is suspended during initial render.

## Reference
- [Source](https://github.com/facebook/react/issues/24669)