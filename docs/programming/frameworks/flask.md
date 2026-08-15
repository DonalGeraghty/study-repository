# Flask

Flask is a Python web framework built around explicit route handlers and a small application core. It is useful for HTTP APIs and compact web services, but production quality still depends on deliberate validation, authentication, persistence, observability, and deployment choices.

## Request Flow

```text
HTTP request -> route -> validation -> application service -> dependency -> response
```

Keep route functions focused on HTTP concerns. Put business rules in ordinary Python modules and isolate databases, model providers, and other external systems behind clear interfaces.

## Application Setup

Small services can create a `Flask` application directly. Larger services benefit from an application factory, blueprints, central error handling, and configuration loaded from environment-specific sources.

Do not enable development debugging in production. Validate required configuration at startup, avoid embedding secrets in source, and use a production-capable serving and hosting arrangement appropriate to the deployment platform.

## API Boundaries

- Parse untrusted JSON defensively and reject unknown or invalid states consistently.
- Return intentional HTTP status codes and stable error representations.
- Treat CORS as a browser access-control policy, not authentication.
- Verify bearer tokens, ownership, and authorisation at every protected boundary.
- Add request identifiers and structured logs without recording credentials or sensitive payloads.

Schema libraries such as Pydantic can validate transport data, but transport models should not become the only place where domain rules live.

## Testing

Use Flask's test client for HTTP behaviour without a live server. Keep pure rules in fast unit tests, test adapters at integration boundaries, and reserve deployed tests for configuration, identity, networking, and platform behaviour.

## Project Connections

The Janus API repositories use Flask and Flask-CORS with Pydantic models, JWT authentication, Firestore, AI-provider SDKs, and Google Cloud deployment.

## Related Guides

- [Python](../languages/python.md)
- [REST APIs](../../quality-engineering/rest-api.md)
- [Test Runners](../../quality-engineering/test-runners.md)
- [Cloud Run](../../platform-engineering/cloud/gcp/cloud-run.md)

Return to [Frameworks and Libraries](./README.md).
