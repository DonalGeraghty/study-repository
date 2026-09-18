---
tags:
  - platform-engineering
---

# Nginx

Nginx is a web server and reverse proxy. In the frontend projects it serves compiled static assets from a small container and provides single-page application route fallback.

## Static Frontend Pattern

```nginx
location /assets/ {
    try_files $uri =404;
}

location / {
    try_files $uri $uri/ /index.html;
}
```

Only application routes should fall back to `index.html`; missing asset requests should remain `404` so deployment mistakes are visible.

## Caching

Fingerprint assets during the build, then cache those immutable names for a long period. Keep the HTML entry point revalidated or short-lived because it selects the current asset versions.

Compression, media types, character encoding, and security headers must match the actual application. Test the resulting headers rather than assuming a copied configuration is correct.

## Container Operation

- Run one foreground server process per container.
- Listen on the platform's expected port.
- Expose a lightweight health endpoint that does not depend on the SPA fallback.
- Keep the image free of source and build dependencies by using a multi-stage build.
- Disable unnecessary server information and writable paths.
- Send access and error logs to the container logging stream where operationally useful.

## Project Connections

Aether builds React assets in a Node stage and serves them from Nginx on Cloud Run. Nyx's deployment workflow creates a similar Nginx image dynamically.

## Interview Questions

> [!question] Interview Questions
> - Why should a missing asset return `404` instead of falling back to `index.html` for a single-page application?
> - Why would you cache fingerprinted asset files aggressively but keep the HTML entry point short-lived or revalidated?
> - Why does a multi-stage build matter for an Nginx image serving a frontend app?
> - What would a lightweight health endpoint need to avoid depending on, and why?
> - Why is it risky to assume a copied Nginx configuration's headers are correct for a new application?

## Related Guides

- [Vite](../programming/tooling/tooling-vite.md)
- [Docker](./platform-engineering-docker.md)
- [Cloud Run](./cloud/gcp/gcp-cloud-run.md)
- [Caching](./platform-engineering-caching.md)

Return to [Platform Engineering](./README.md).
