# Node.js and npm

Node.js is a JavaScript runtime outside the browser. npm installs packages and runs repository-defined scripts from `package.json`.

## Runtime Model

Node.js runs JavaScript on an event loop and delegates many I/O operations to the operating system or worker pool. Asynchronous I/O supports many concurrent connections, but CPU-heavy synchronous work can still block progress for every request in the process.

Handle promises deliberately, apply timeouts and cancellation where supported, and close servers, database pools, and other resources during shutdown.

## Package Manifests and Locks

- `dependencies` are required by the running application.
- `devDependencies` support building, checking, or testing.
- `scripts` define repeatable project commands.
- `engines` communicates supported runtime versions but is not always enforced automatically.
- `package-lock.json` records the resolved dependency graph for reproducible npm installs.

Use `npm ci` in CI when a committed lockfile is authoritative. It installs the locked graph and fails when the manifest and lock disagree. Keep the runtime version aligned across local development, CI, and containers.

### Example Manifest

```json
{
  "name": "result-api",
  "private": true,
  "type": "module",
  "engines": {
    "node": ">=22 <23"
  },
  "scripts": {
    "dev": "node --watch src/server.js",
    "start": "node src/server.js",
    "test": "node --test",
    "check": "npm run test"
  },
  "dependencies": {
    "express": "5.1.0"
  }
}
```

`private` prevents accidental publication. The scripts are the project's public command interface: local development and CI should call them instead of duplicating tool-specific commands. The version is illustrative; update dependencies through the repository's normal review process.

```bash
npm ci
npm run check
npm start
```

## Modules and Configuration

Projects may use ECMAScript modules (`import`) or CommonJS (`require`). The module type, file extensions, dependencies, and runtime version must agree.

Treat environment variables as untrusted configuration. Validate required values at startup and never place server secrets in frontend build variables; bundlers expose compiled client configuration to users.

## Asynchronous Failure Example

```javascript
export async function loadResult(id, signal) {
  const response = await fetch(`https://example.test/results/${id}`, {
    signal,
    headers: { accept: "application/json" }
  });

  if (!response.ok) {
    throw new Error(`result request failed: ${response.status}`);
  }

  return response.json();
}
```

The caller owns the timeout or cancellation policy and handles the rejected promise. Avoid starting background promises whose failures no request, job, or shutdown boundary observes.

## Common Failure Modes

- mixing CommonJS and ECMAScript modules without an explicit boundary;
- using `npm install` in CI and silently changing the locked graph;
- putting a runtime package in `devDependencies`;
- blocking the event loop with CPU-heavy loops or synchronous file operations in request paths;
- logging environment variables or tokens during configuration errors;
- exiting before servers, workers, or connection pools finish a bounded shutdown.

## Project Connections

Nyx and Aether use npm to build React applications with Vite. `shtormscsgo` uses Vue CLI, Cypress uses its Node package, and `tododos-express-api` runs an Express server on Node.js.

## Related Guides

- [JavaScript and TypeScript](../languages/javascript-typescript.md)
- [Express](../frameworks/express.md)
- [Vite](./vite.md)
- [Docker](../../platform-engineering/docker.md)

Return to [Development Tooling](./README.md).
