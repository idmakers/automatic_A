---
title: "Functions and buttons from react-big-calendar don't work in next 13.4.9 and upwards"
tags:
  - React Big Calendar
  - Next.js
  - Bug Fix
---

# Broken Buttons in React Big Calendar with Next.js 13.4.9+

## Core Problem

Users of Next.js 13.4.9+ may experience issues with functions and buttons from react-big-calendar not working as expected. The problem persists even when using the latest canary release, indicating an upstream issue.

## Solution & Analysis

### Investigating the Issue
The related PR is https://github.com/vercel/next.js/pull/52282. This suggests that the problem lies in React itself, rather than Next.js. To verify this, we need to isolate the issue.

### Verifying with Experimental Versions
By installing `0.0.0-experimental-7118f5dd7-20230705` of `react` and `react-dom`, and moving the page to `pages`, we can confirm if it's a React-related issue.

```bash
npm install react@experimental react-dom@experimental --save-dev
```

Then, in your next.js project:

```javascript
// pages/index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Calendar } from '@date-io/react-big-calendar';

const App = () => {
  return (
    <div>
      <Calendar />
    </div>
  );
};

export default App;
```

## Conclusion

The issue with functions and buttons from react-big-calendar not working in Next.js 13.4.9+ appears to be a React-related problem, rather than an issue with the framework itself. By installing experimental versions of React and verifying this is indeed a React issue, we can identify potential solutions or workarounds that may involve using different versions or patches of React for Next.js applications.

## Reference
- [Source](https://github.com/vercel/next.js/issues/56206)