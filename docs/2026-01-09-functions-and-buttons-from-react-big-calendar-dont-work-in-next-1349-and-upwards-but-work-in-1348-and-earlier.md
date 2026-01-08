# React Big Calendar Buttons Break in Next.js 13.4.9 and Upwards

## Core Problem

Users of Next.js 13.4.9 and upwards are experiencing issues with the functions and buttons from `react-big-calendar`. Despite using the same code, the buttons do not work as expected, whereas previous versions (13.4.8 and earlier) function correctly.

## Solution & Analysis

The issue can be reproduced by starting the application with `npm run dev` and trying to click any of the buttons from the home page in the toolbar. In this case, no button works, unlike the expected behavior.

To verify that this is a React issue, we can try installing `0.0.0-experimental-7118f5dd7-20230705` of `react` and `react-dom` and moving the page to `pages`. This approach allows us to isolate the problem and determine whether it's related to Next.js or React.

Here is an example of how you can try this:

```bash
npm install 0.0.0-experimental-7118f5dd7-20230705 react react-dom
```

Then, create a new file in your project directory called `pages/index.js` and add the following code:
```jsx
import React from 'react';
import { Calendar } from 'react-big-calendar';

const MyCalendar = () => {
  return (
    <div>
      <Calendar />
    </div>
  );
};

export default MyCalendar;
```

If this solution works, it suggests that the problem is related to Next.js and its handling of React. In contrast, using a specific version of `react` can help isolate the issue.

## Conclusion

In conclusion, the functions and buttons from `react-big-calendar` are not working in Next.js 13.4.9 and upwards due to an upstream issue with React. By trying different approaches to isolate the problem, we were able to determine that this is a React-related issue rather than a Next.js-specific problem.

To resolve this issue, you may want to consider using a different version of Next.js or exploring alternative solutions for integrating `react-big-calendar` into your application.

### References

- [Related PR](https://github.com/vercel/next.js/pull/52282)
- [Bug Report](https://github.com/octavian-regatun/nextjs-react-big-calendar-with-bug)

## Reference
- [Source](https://github.com/vercel/next.js/issues/56206)