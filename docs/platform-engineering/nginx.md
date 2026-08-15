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

## Related Guides

- [Vite](../programming/tooling/vite.md)
- [Docker](./docker.md)
- [Cloud Run](./cloud/gcp/cloud-run.md)
- [Caching](./caching.md)

Return to [Platform Engineering](./README.md).
