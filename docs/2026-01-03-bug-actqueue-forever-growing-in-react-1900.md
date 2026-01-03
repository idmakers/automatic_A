---
title: "React 19.0.0 Act Queue Forever Growing Issue"
tags:
  - React 19.0.0
  - actQueue
  - infinite loop
---

# Understanding the Act Queue Issue in React 19.0.0

## Core Problem
When migrating from React 18.3.1 to React 19.0.0, users encountered a unit test failure caused by an infinite loop in the act queue. This issue affects components with `<Suspense />` and `react.lazy`, particularly when using a component with a ref that depends on state.

## Solution & Analysis
To debug this issue, we need to identify the root cause of the problem. Based on the provided information, the following potential solutions were explored:

### Removing unnecessary dependencies
1. **Removing `setRef` callsite from ref props**: By doing so, the test can finish successfully.
```jsx
// Before
const [ref, setRef] = useState(null);
return (
  <div ref={setRef} />
);

// After
return (
  <div />
);
```
2. **Removing `react.lazy` and `<Suspense>`**: These components seem to be contributing to the infinite loop.
```jsx
// Before
const LazyComponent = react.lazy(() => import('./LazyComponent'));

function ParentComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    </div>
  );
}

// After
function ParentComponent() {
  return <div />;
}
```
3. **Removing the ref state dependency**: By removing this dependency, the test can still finish without entering an infinite loop.
```jsx
// Before
const [ref, setRef] = useState(null);
return (
  <div ref={setRef} />
);

// After
function Component() {
  return <div />;
}
```
### Code example with actQueue analysis
To further analyze the issue, we can use the `act()` function from React Testing Library to inspect the act queue:
```jsx
import { act } from '@testing-library/react';

test('should render', () => {
  const component = render(<Component />);
  expect(component).toBeTruthy();
});

beforeEach(() => {
  jest.clearAllMocks();
  jest.clearAllTimers();
});

afterEach(() => {
  jest.restoreAllMocks();
  jest.restoreAllTimers();
});

it('should not enter infinite loop', async () => {
  const actQueue = getActQueue();
  act(() => {
    // Simulate some asynchronous operation
    await new Promise((resolve) => setTimeout(resolve, 100));
    // Add another action to the queue
    act(() => {
      // Simulate some other asynchronous operation
      await new Promise((resolve) => setTimeout(resolve, 100));
    });
  });
  expect(actQueue.length).toBeGreaterThan(0);
});
```
### Conclusion
In conclusion, this issue highlights the importance of carefully analyzing the dependencies and actions in React components when migrating to a new version. By identifying and removing unnecessary dependencies or optimizing the ref state usage, we can prevent infinite loops and ensure smooth testing and development experiences.

<!-- ADSENSE_PLACEHOLDER -->