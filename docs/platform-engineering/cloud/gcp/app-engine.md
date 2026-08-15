# App Engine

App Engine is a managed Google Cloud application platform. The standard environment runs supported language runtimes in a constrained, automatically managed service model; the flexible environment uses a more customisable container-based model.

## Deployment Model

An `app.yaml` declares runtime and service configuration. A deployment creates a version, and traffic can be directed or split between versions.

```text
source + app.yaml -> App Engine version -> service -> incoming traffic
```

Keep durable state outside application instances. Instances can start, stop, and scale, so local files and in-memory values are not a database or reliable coordination mechanism.

## Design Considerations

- Confirm runtime support and platform constraints before selecting an environment.
- Use service identities and least-privilege IAM for downstream Google APIs.
- Set scaling bounds with latency, concurrency, and cost in mind.
- Treat warmup, startup, shutdown, and instance replacement as normal lifecycle events.
- Store secrets in an appropriate secret system rather than `app.yaml` or source.
- Create new versions for change and define traffic migration and rollback procedures.

## Testing and Operations

Test runtime-version compatibility, request limits, timeouts, health, permissions, and downstream failures. Monitor version traffic, instance count, latency, errors, and cost. Remove obsolete versions only after rollback and retention needs are satisfied.

## Project Connections

`penguin-spring-api` contains an App Engine standard `app.yaml` for a Java 11 Spring service and uses Google Cloud Pub/Sub.

## Related Guides

- [Spring](../../../programming/frameworks/spring.md)
- [Google Cloud Platform](./README.md)
- [Publish/Subscribe](../../pub-sub.md)
- [IAM](./iam.md)

Return to [Google Cloud Platform](./README.md).
