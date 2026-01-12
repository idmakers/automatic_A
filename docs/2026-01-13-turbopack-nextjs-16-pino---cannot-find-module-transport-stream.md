# Turbopack Next.js 16: Pino - Cannot find module './transport-stream'

**Summary:** Discussions in this issue have diverged a bit, and there are mainly two cases:
1. **Turbopack + non-Vercel deployment.**
2. **Turbopack + Vercel deployment.**

## Current behavior

With Turbopack in NextJs v16, Pino worker thread throws the following error:

```
Uncaught Exception: Error: Cannot find module './transport-stream'
Require stack:
- /var/task/node_modules/.pnpm/pino@10.1.0/node_modules/pino/lib/worker.js
    at Module._resolveFilename (node:internal/modules/cjs/loader:1420:15)
    at defaultResolveImpl (node:internal/modules/cjs/loader:1058:19)
    at resolveForCJSWithHooks (node:internal/modules/cjs/loader:1063:22)
    at Module._load (node:internal/modules/cjs/loader:1226:37)
    at TracingChannel.traceSync (node:diagnostics_channel:328:14)
    at wrapModuleLoad (node:internal/modules/cjs/loader:244:24)
    at Module.require (node:internal/modules/cjs/loader:1503:12)
    at a (/opt/rust/bytecode.js:2:1094)
    at Object.<anonymous> (/var/task/node_modules/.pnpm/pino@10.1.0/node_modules/pino/lib/worker.js:7:36)
    at Module.<anonymous> (/opt/rust/bytecode.js:2:1435)
```

## Expected behavior

Pino worker thread works without any exception, which is the behavior of using WebPack.

## Solution & Analysis
To fix this issue, you can follow these steps:

### Step 1: Add `pino`, `thread-stream` and related pino transport target packages to `serverExternalPackages`

You might also need to add `thread-stream` as a dependency explicitly. Here's an example of how to do it in your `next.config.js` file:
```javascript
module.exports = {
  // ...
  serverExternalPackages: [
    'pino',
    'thread-stream',
    // Add other required packages here
  ],
};
```
### Step 2: Install `thread-stream`

You can install `thread-stream` by running the following command in your terminal:
```bash
npm install thread-stream
```
Or, if you're using yarn:
```bash
yarn add thread-stream
```
## Conclusion

By adding `pino`, `thread-stream` and related packages to `serverExternalPackages`, you should be able to fix the issue with Pino worker thread throwing an error. Make sure to also install `thread-stream` as a dependency.

Additional context: This issue started happening with Next.js 16.0.3, and it seems that Turbopack is not able to properly handle multiple targets, including Pino.

## Reference
- [Source](https://github.com/vercel/next.js/issues/86099)