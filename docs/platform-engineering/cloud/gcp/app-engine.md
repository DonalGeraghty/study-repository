# App Engine

App Engine is a managed Google Cloud application platform. The standard environment runs supported language runtimes in a constrained, automatically managed service model; the flexible environment uses a more customisable container-based model.

## Deployment Model

An `app.yaml` declares runtime and service configuration. A deployment creates a version, and traffic can be directed or split between versions.

```text
source + app.yaml -> App Engine version -> service -> incoming traffic
```

Keep durable state outside application instances. Instances can start, stop, and scale, so local files and in-memory values are not a database or reliable coordination mechanism.

## Example Configuration and Release

A small standard-environment service has a committed `app.yaml`:

```yaml
runtime: java21
service: result-api

automatic_scaling:
  min_instances: 0
  max_instances: 4

env_variables:
  LOG_LEVEL: INFO
```

Non-secret environment settings may live in deployment configuration. Credentials and private keys should come from an appropriate secret and identity mechanism rather than this file.

Create a named version without immediately moving production traffic:

```bash
gcloud app deploy app.yaml \
  --version=git-a1b2c3d \
  --no-promote
```

Smoke-test the new version through its version-specific endpoint, then migrate traffic deliberately. A rollback changes traffic to a known good version; it does not undo an incompatible database migration or external side effect.

## Design Considerations

- Confirm runtime support and platform constraints before selecting an environment.
- Use service identities and least-privilege IAM for downstream Google APIs.
- Set scaling bounds with latency, concurrency, and cost in mind.
- Treat warmup, startup, shutdown, and instance replacement as normal lifecycle events.
- Store secrets in an appropriate secret system rather than `app.yaml` or source.
- Create new versions for change and define traffic migration and rollback procedures.

## Testing and Operations

Test runtime-version compatibility, request limits, timeouts, health, permissions, and downstream failures. Monitor version traffic, instance count, latency, errors, and cost. Remove obsolete versions only after rollback and retention needs are satisfied.

## Common Failure Modes

- writing required state to the instance filesystem;
- promoting a version before its identity, configuration, and dependencies are checked;
- placing secrets in `app.yaml`;
- assuming automatic scaling protects an overloaded database;
- deleting the previous version before the rollback window closes;
- coupling deployment rollback to an irreversible schema change.

## Project Connections

`penguin-spring-api` contains an App Engine standard `app.yaml` for a Java 11 Spring service and uses Google Cloud Pub/Sub.

## Official References

- [App Engine standard Java runtime](https://cloud.google.com/appengine/docs/standard/java-gen2/runtime)
- [`gcloud app deploy`](https://cloud.google.com/sdk/gcloud/reference/app/deploy)

## Related Guides

- [Spring](../../../programming/frameworks/spring.md)
- [Google Cloud Platform](./README.md)
- [Publish/Subscribe](../../pub-sub.md)
- [IAM](./iam.md)

Return to [Google Cloud Platform](./README.md).
