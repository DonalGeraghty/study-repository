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

## Checks Around the Build

ESLint catches selected correctness and consistency problems before runtime. A test runner such as Vitest can share Vite's transformation model, while DOM-oriented libraries exercise rendered behaviour in a simulated browser environment. These tools complement rather than replace production-build and browser checks.

## Project Connections

Nyx and Aether use Vite with React plugins. Nyx integrates Vitest and Testing Library; Aether uses ESLint and Node's built-in test runner. Both produce static frontend assets, while Aether serves them from Nginx in a multi-stage Docker image.

## Related Guides

- [React](../frameworks/react.md)
- [Node.js and npm](./nodejs-and-npm.md)
- [Test Runners](../../quality-engineering/test-runners.md)
- [Nginx](../../platform-engineering/nginx.md)

Return to [Development Tooling](./README.md).
