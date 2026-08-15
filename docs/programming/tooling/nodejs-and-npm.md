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

## Modules and Configuration

Projects may use ECMAScript modules (`import`) or CommonJS (`require`). The module type, file extensions, dependencies, and runtime version must agree.

Treat environment variables as untrusted configuration. Validate required values at startup and never place server secrets in frontend build variables; bundlers expose compiled client configuration to users.

## Project Connections

Nyx and Aether use npm to build React applications with Vite. `shtormscsgo` uses Vue CLI, Cypress uses its Node package, and `tododos-express-api` runs an Express server on Node.js.

## Related Guides

- [JavaScript and TypeScript](../languages/javascript-typescript.md)
- [Express](../frameworks/express.md)
- [Vite](./vite.md)
- [Docker](../../platform-engineering/docker.md)

Return to [Development Tooling](./README.md).
