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

### Application Factory Example

```python
from flask import Flask, jsonify


def create_app(result_service):
    app = Flask(__name__)

    @app.get("/results/<int:result_id>")
    def get_result(result_id):
        result = result_service.find(result_id)
        if result is None:
            return jsonify(
                type="result-not-found",
                title="Result not found",
            ), 404
        return jsonify(result)

    @app.errorhandler(Exception)
    def handle_unexpected_error(error):
        app.logger.exception("request failed")
        return jsonify(
            type="internal-error",
            title="The request could not be completed",
        ), 500

    return app
```

Injecting the service keeps persistence and provider setup outside the route. Register more routes through blueprints as the application grows rather than turning one factory into the whole system.

## API Boundaries

- Parse untrusted JSON defensively and reject unknown or invalid states consistently.
- Return intentional HTTP status codes and stable error representations.
- Treat CORS as a browser access-control policy, not authentication.
- Verify bearer tokens, ownership, and authorisation at every protected boundary.
- Add request identifiers and structured logs without recording credentials or sensitive payloads.

Schema libraries such as Pydantic can validate transport data, but transport models should not become the only place where domain rules live.

## Testing

Use Flask's test client for HTTP behaviour without a live server. Keep pure rules in fast unit tests, test adapters at integration boundaries, and reserve deployed tests for configuration, identity, networking, and platform behaviour.

```python
def test_missing_result_returns_404():
    class MissingResultService:
        def find(self, result_id):
            return None

    app = create_app(MissingResultService())
    client = app.test_client()

    response = client.get("/results/730")

    assert response.status_code == 404
    assert response.json["type"] == "result-not-found"
```

## Common Failure Modes

- using Flask's development server or debugger in production;
- relying on module-level mutable state across requests;
- exposing exception text or stack traces in HTTP responses;
- accepting JSON without content, shape, and size validation;
- trusting a decoded token without verifying its signature, claims, and authorisation context;
- testing only through a deployed server and making simple rules slow to diagnose.

## Project Connections

The Janus API repositories use Flask and Flask-CORS with Pydantic models, JWT authentication, Firestore, AI-provider SDKs, and Google Cloud deployment.

## Related Guides

- [Python](../languages/python.md)
- [REST APIs](../../quality-engineering/rest-api.md)
- [Test Runners](../../quality-engineering/test-runners.md)
- [Cloud Run](../../platform-engineering/cloud/gcp/cloud-run.md)

Return to [Frameworks and Libraries](./README.md).
