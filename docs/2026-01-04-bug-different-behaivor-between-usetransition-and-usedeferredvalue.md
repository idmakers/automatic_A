---
title: "Different Behaivor between useTransition and useDeferredValue in React"
tags:
  - React Hooks
  - State Management
  - Transition Optimization
---

# Understanding the Difference Between `useTransition` and `useDeferredValue`

## Core Problem

When using React Hooks to manage state transitions, developers may notice a difference in behavior between `useTransition` and `useDeferredValue`. Specifically, when updating states that are not currently in transition, `useTransition` can cause UI blocking, while `useDeferredValue` does not exhibit this issue. In this article, we will explore the reasons behind these differences and provide guidance on how to optimize state transitions in React.

## Solution & Analysis

The main difference between `useTransition` and `useDeferredValue` lies in their approach to managing state updates during a transition.
```jsx
import { useState, useTransition } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  const transitions = useTransition();

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <p>Count: {count}</p>
      <Post />
      {transitions.state === 'pending' && (
        <div>Transition in progress...</div>
      )}
    </div>
  );
}
```
In the `Counter` component, we use `useTransition` to manage state updates. When the button is clicked, we increment the `count` state and trigger a transition using `transitions.useUpdate`. However, when we update other states that are not in transition (e.g., the `Post` component), it can cause UI blocking.

On the other hand, `useDeferredValue` does not exhibit this issue.
```jsx
import { useState } from 'react';

function Post() {
  const [post, setPost] = useState('');
  const deferredValue = useState('Initial Value');

  return (
    <div>
      <input type="text" value={deferredValue[0]} onChange={(e) => setDeferredValue(e.target.value)} />
      <p>Post: {post}</p>
    </div>
  );
}
```
In the `Post` component, we use `useDeferredValue` to manage state updates. When the user types in the input field, we update the `deferredValue` state using `setDeferredValue`. This change is not reflected immediately and does not cause UI blocking.

The reason for this difference lies in how each hook handles state updates during a transition.
```jsx
import { useTransition } from 'react';

function useTransition() {
  const [state, setState] = useState('idle');

  return (update) => {
    if (state === 'pending') {
      update(() => {
        setState('idle');
      });
    }
  };
}
```
`useTransition` blocks the UI when a transition is in progress and updates other states. This is because it waits for the transition to complete before updating the state.

In contrast, `useDeferredValue` defers state updates until the next frame.
```jsx
import { useState } from 'react';

function useDeferredValue(initialValue) {
  const [value, setValue] = useState(initialValue);

  return (update) => {
    return () => {
      setValue(update);
    };
  };
}
```
When `useDeferredValue` updates a state, it defers the change until the next frame. This allows other states to be updated concurrently without causing UI blocking.

## Conclusion

In conclusion, the difference between `useTransition` and `useDeferredValue` lies in their approach to managing state updates during a transition. While `useTransition` can cause UI blocking when updating states that are not in transition, `useDeferredValue` defers state updates until the next frame, allowing for concurrent updates without blocking the UI. By understanding these differences, developers can optimize their state transitions and create more responsive user interfaces.

## Reference
- [Source](https://github.com/facebook/react/issues/34408)