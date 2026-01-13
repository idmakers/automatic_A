# Turbopack Next.js 16: Pino - Cannot find module './transport-stream'

## Core Problem

With the latest version of Next.js 16 and Turbopack, users are encountering an error when using Pino for logging. The issue is caused by Turbopack's inability to properly handle the `pino/lib/worker.js` file, leading to a "Cannot find module './transport-stream'" error.

## Solution & Analysis

### For Non-Vercel Deployment

To resolve this issue, you can try the following workarounds:

1. Switch back to Webpack: `next build --webpack`
2. Add `pino`, `thread-stream` and related pino transport target packages to `serverExternalPackages`.
	* Set `serverExternalPackages`: `next.config.js` configuration file.
	* Install `thread-stream`: Run `npm install thread-stream` or `yarn add thread-stream`
	* Use shim: Add a shim in your `next.config.js` file to specify the version of `thread-stream`

Example:
```javascript
module.exports = {
  // ...
  serverExternalPackages: ['pino', 'thread-stream'],
  plugins: [
    require('next-shim')({
      threadStream: true,
    }),
  ],
};
```
### For Vercel Deployment

Unfortunately, the fixes for Turbopack + non-Vercel deployment do not apply to Turbopack + Vercel deployment. The only workaround is to use Webpack `next build --webpack` with `serverExternalPackages`.

Example:
```bash
next build --webpack --serverExternalPackages=pino,thread-stream
```
## Conclusion

To avoid this issue in Next.js 16 and Turbopack, consider using a different logging library or adjusting your configuration to work around the limitation.

## Reference
- [Source](https://github.com/vercel/next.js/issues/86099)