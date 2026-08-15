# Cloud Run

Cloud Run is a managed application platform for running code in containers without administering a cluster. It supports request-driven services, run-to-completion jobs, and worker pools for always-on pull-based workloads.

## Resource Model

Choose the resource type from how work begins and ends:

| Resource | Use |
| --- | --- |
| Service | Handles HTTP, gRPC, events, or functions through a stable endpoint |
| Job | Performs finite work and exits successfully or unsuccessfully |
| Worker pool | Runs continuous non-request, pull-based processing |

A service deployment creates an immutable revision. Configuration changes create new revisions, and traffic can be shifted gradually or rolled back between revisions.

```text
container image -> revision -> Cloud Run service -> HTTPS requests
                              ├── old revision: 90%
                              └── new revision: 10%
```

## Container Contract

A service container must listen on `0.0.0.0` using the injected `PORT` value. A job should perform its task and exit with code `0` on success or non-zero on failure.

The writable container filesystem is disposable and consumes instance memory. Store durable data in Cloud Storage, Firestore, a database, or another external service.

Applications should:

- start quickly and expose a startup probe when appropriate;
- handle concurrent requests safely;
- set application and downstream timeouts;
- handle `SIGTERM` and stop cleanly;
- avoid assuming an instance will remain alive;
- make retries safe through idempotency where required.

## Scaling and Concurrency

Services can scale from zero according to traffic and resource demand. Minimum instances can reduce cold-start latency at additional cost; maximum instances can protect budgets or constrained dependencies.

Concurrency determines how many requests an instance may process simultaneously. Higher concurrency can reduce instance count but increases shared CPU, memory, connection, and thread pressure. Tune from measured application behaviour rather than accepting a value blindly.

Scaling the service does not automatically scale its database connection limit, external API quota, or downstream capacity. Bound concurrency and connection pools accordingly.

## Identity and Access

Each revision runs as a service account. Grant that runtime identity only the permissions needed to call other services. Do not use the default service account merely for convenience.

Separately control:

- who may deploy or change the service;
- who may act as the runtime service account;
- who may invoke a private service;
- which resources the running service may access.

Public reachability, network ingress, and IAM invocation are distinct decisions. A private URL alone does not replace authorisation inside a multi-user application.

## Configuration and Secrets

Environment variables suit non-secret runtime configuration. Store secrets in Secret Manager or another controlled secret system and grant access to the runtime identity. A new configuration creates a revision, which makes configuration changes deployable and reversible.

Do not embed credentials in the image, source repository, command history, or ordinary logs.

## Networking

Cloud Run can reach public endpoints and Google APIs and can be configured for VPC egress to private resources. Ingress settings restrict which network paths may reach a service.

Document:

- public, internal, or load-balancer ingress;
- VPC egress and subnet capacity;
- DNS and private service resolution;
- internet egress and fixed-address requirements;
- database connection routing;
- service-to-service authentication.

## Delivery and Observability

A typical delivery flow is:

```text
source -> Cloud Build -> tested image in Artifact Registry
       -> deploy revision with no or limited traffic
       -> verify health and user outcomes
       -> increase traffic or roll back
```

Send structured logs to standard output and error. Monitor request count, latency, status codes, container startup, instance count, CPU, memory, and downstream failures. Alerts should indicate user impact or exhausted capacity rather than every instance change.

## Testing

Test the container locally and in a representative Cloud Run environment. Verify startup, health, concurrency, cancellation, timeouts, authentication, permission denial, scale-from-zero behaviour, graceful shutdown, and dependency limits.

For jobs, test retry safety, task partitioning, partial failure, exit codes, and resumability.

## Readiness Checklist

You should be able to:

- choose between a service, job, and worker pool;
- satisfy the container runtime contract;
- tune concurrency, minimum instances, and maximum instances from constraints;
- separate deployment, invocation, and runtime identities;
- design ingress, egress, configuration, and secret handling;
- deliver revisions progressively and roll back safely;
- diagnose cold starts, resource limits, and downstream saturation.

## Official References

- [What is Cloud Run](https://cloud.google.com/run/docs/overview/what-is-cloud-run)
- [Container runtime contract](https://cloud.google.com/run/docs/container-contract)
- [Configure Cloud Run services](https://cloud.google.com/run/docs/configuring)
- [Cloud Run security](https://cloud.google.com/run/docs/securing/security)

Return to [Google Cloud Platform](./README.md).
