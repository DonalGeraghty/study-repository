---
tags:
  - programming/frameworks
---

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
from werkzeug.exceptions import HTTPException


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
        if isinstance(error, HTTPException):
            return error
        app.logger.exception("request failed")
        return jsonify(
            type="internal-error",
            title="The request could not be completed",
        ), 500

    return app
```

Injecting the service keeps persistence and provider setup outside the route. Register more routes through blueprints as the application grows rather than turning one factory into the whole system.

The `HTTPException` branch preserves framework-generated responses such as 404 and 405. Without it, the broad handler would turn an unknown route or wrong HTTP method into a misleading 500. These pass-through responses retain Flask's default representation; register a specific HTTP-error formatter if the API requires JSON throughout.

## API Boundaries

Parse untrusted JSON defensively, reject unknown or invalid states consistently, and return intentional HTTP status codes with stable error representations.

Treat CORS as a browser access-control policy rather than authentication. Verify bearer tokens, ownership and authorisation at every protected boundary.

Request identifiers and structured logs help explain failures, but should not record credentials or sensitive payloads.

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

## Worked Prediction: Error Boundaries

Using the factory above, predict each outcome with a fake service:

| Request or dependency behaviour | Expected result |
| --- | --- |
| Valid result ID; service returns a record | 200 with that record |
| Valid result ID; service returns `None` | 404 with `result-not-found` |
| Unknown URL or non-integer route segment | Framework 404, preserved by the handler |
| POST to the GET-only route | 405, preserving the method contract |
| Service raises an unexpected exception | Generic 500; details remain in server logs |

**Check your reasoning:** Routing failure, missing domain data, and infrastructure failure are different outcomes. A blanket catch that maps all of them to 500 hides that distinction. Extend the test-client example to check those status codes and verify that a private exception message never appears in the body. Also assert that invalid routes never call the service.

## Interview Questions

> [!question] Interview Questions
> - Why should route functions stay focused on HTTP concerns instead of holding business rules directly?
> - Why doesn't validating a JWT's signature alone prove a request is authorised?
> - What's the risk of relying on module-level mutable state across requests?
> - Why would you use Flask's test client instead of testing only against a deployed server?

## Answer Notes

1. Routes should translate HTTP input into application calls and map results back to responses. Keeping business rules in separate functions or services makes them reusable and testable without requiring an HTTP request.

2. A valid signature only establishes that the token was signed by a trusted key. Validate relevant claims such as expiry, issuer and audience, then separately enforce access to the requested action and resource.

3. Requests may run concurrently, and different worker processes hold different copies of module state. This can produce races and inconsistent results; use request-scoped data or an appropriate shared durable store.

4. The test client exercises routing, validation and response behaviour without starting a network server. It gives fast controlled checks, while deployment, networking and production server behaviour still need suitable integration coverage.

## Official References

- [Flask error handling](https://flask.palletsprojects.com/en/stable/errorhandling/)
- [Flask testing](https://flask.palletsprojects.com/en/stable/testing/)

## Related Guides

- [Python](../languages/python.md)
- [REST APIs](../../quality-engineering/rest-api.md)
- [Test Runners](../../quality-engineering/test-runners.md)
- [Cloud Run](../../platform-engineering/cloud/gcp/gcp-cloud-run.md)

Return to [Frameworks and Libraries](./README.md).
