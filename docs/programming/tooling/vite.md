# Vite and Frontend Tooling

Vite provides a development server and an optimised production build for modern web projects. Framework plugins transform source such as React JSX while the build emits static assets for a browser or static web server.

## Development and Production

```text
source modules -> Vite development server -> browser
source modules -> production build       -> versioned static assets
```

Development-server success does not prove that the production build, base path, static server fallback, or runtime API configuration is correct. Run the production build in CI and smoke-test the emitted application through its intended server.

## Configuration

- Keep configuration small and committed with the source.
- Expose only intentionally public frontend environment values.
- Use a consistent Node.js version and a locked package graph.
- Preserve source maps according to debugging and disclosure requirements.
- Configure single-page application fallback in the web server, not only the development server.
- Apply long-lived caching to fingerprinted assets and revalidation to the HTML entry point.

### Example Configuration

```javascript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  build: {
    sourcemap: true
  }
});
```

A browser module can read explicitly exposed Vite configuration:

```javascript
const apiBaseUrl = import.meta.env.VITE_API_BASE_URL;

if (!apiBaseUrl) {
  throw new Error("VITE_API_BASE_URL is required");
}
```

Values with the public prefix are compiled into client assets and can be inspected by users. They may contain an API URL or public feature flag, never a database password, signing key, or private provider token.

## Checks Around the Build

ESLint catches selected correctness and consistency problems before runtime. A test runner such as Vitest can share Vite's transformation model, while DOM-oriented libraries exercise rendered behaviour in a simulated browser environment. These tools complement rather than replace production-build and browser checks.

```bash
npm ci
npm run lint
npm run test
npm run build
npm run preview -- --host 127.0.0.1
```

`preview` is useful for inspecting a build locally; production behaviour must still be tested with the real base path, headers, caching, routing fallback, and runtime platform.

## Common Failure Modes

- assuming the development server reproduces the production server;
- placing secrets in `VITE_*` variables;
- deploying with the wrong `base` path and breaking asset URLs;
- routing every missing asset to `index.html` and hiding deployment errors;
- caching `index.html` as immutable while it points to newer asset names;
- skipping the production build in CI.

## Project Connections

Nyx and Aether use Vite with React plugins. Nyx integrates Vitest and Testing Library; Aether uses ESLint and Node's built-in test runner. Both produce static frontend assets, while Aether serves them from Nginx in a multi-stage Docker image.

## Related Guides

- [React](../frameworks/react.md)
- [Node.js and npm](./nodejs-and-npm.md)
- [Test Runners](../../quality-engineering/test-runners.md)
- [Nginx](../../platform-engineering/nginx.md)

Return to [Development Tooling](./README.md).
