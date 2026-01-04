---
title: "Different Behavior between useTransition and useDeferredValue in React"
tags:
  - React Hooks
  - State Management
  - Transitioning

---

# Different Behavior between useTransition and useDeferredValue in React

## Core Problem
When using React Hooks, the `useTransition` hook and `useDeferredValue` hook behave differently when interrupting high-priority state updates. This inconsistency can lead to unexpected UI behavior and performance issues.

## Solution & Analysis
The key difference lies in how these hooks handle concurrent updates.

### useTransition

`useTransition` is designed to not block the UI during transitions. It uses a worklet queue to manage the transition's execution, allowing for asynchronous updates without blocking the main thread. However, if the transition is ongoing and you update other states that are not part of the transition, it will interrupt the transition as expected.

On the other hand, when you constantly update states that are not in transition (e.g., updating the `counter` state before the transition finishes), it can cause a temporary UI freeze. This happens because React is re-rendering the component with the new state values, which can lead to unnecessary re-renders and potential performance issues.

```jsx
import { useTransition } from '@reactjsi/react-hooks';

function Sandbox() {
  const [counter, setCounter] = React.useState(0);
  const [isTransitioning, setIsTransitioning] = React.useState(false);

  const transition = useTransition(isTransitioning, () => ({}), {
    animation: 'fade',
    children: 'Transitioning...',
  });

  React.useEffect(() => {
    if (!transition.isRunning) {
      setCounter(counter + 1);
    }
  }, [counter]);

  return (
    <div>
      <button onClick={() => setIsTransitioning(true)}>Start Transition</button>
      <PostTab />
      <Counter value={counter} onChange={(newCount) => setCounter(newCount)} />
    </div>
  );
}
```

In the above code, we have a `Counter` component that updates its state constantly. When the transition is not running, it updates the `counter` state immediately. However, when the transition is running, updating the `counter` state will interrupt the transition.

### useDeferredValue

On the other hand, `useDeferredValue` attaches a value to the `render()` method, which means that it will always be interrupted until the `render()` method stops running or a new render is requested. This behavior ensures that updates are batched together and executed efficiently.

```jsx
import { useDeferredValue } from '@reactjsi/react-hooks';

function Sandbox() {
  const [counter, setCounter] = React.useState(0);
  const deferredValue = React.useDeferredValue(counter);

  return (
    <div>
      <button onClick={() => setCounter(deferredValue + 1)}>Increment</button>
      <PostTab />
      <Counter value={deferredValue} onChange={(newCount) => setCounter(newCount)} />
    </div>
  );
}
```

In the above code, we use `useDeferredValue` to attach a value to the `render()` method. When we update the `counter` state and call `setCounter()`, it will batch the update together with any other updates that are attached to the `render()` method.

## Conclusion
The different behavior between `useTransition` and `useDeferredValue` can be attributed to their design goals and implementation differences. While `useTransition` is designed to not block the UI during transitions, it may lead to temporary UI freezes if concurrent updates occur. On the other hand, `useDeferredValue` ensures that updates are batched together and executed efficiently by interrupting its value until the `render()` method stops running or a new render is requested.

To avoid unexpected behavior, consider using `useTransition` with caution when updating states concurrently, and use `useDeferredValue` for attaching values to the `render()` method.

## Reference
- [Source](https://github.com/facebook/react/issues/34408)