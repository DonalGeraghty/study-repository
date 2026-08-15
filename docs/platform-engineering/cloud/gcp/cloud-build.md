# Cloud Build

Cloud Build executes build steps on managed Google Cloud infrastructure. It can retrieve source, run tests and analysis, build packages or container images, publish artifacts, and trigger deployments.

## Build Model

A build consists of ordered or explicitly dependent steps. Each step runs in a container image and shares the build workspace with other steps.

```text
source event -> build trigger -> ephemeral build environment
                              ├── compile
                              ├── test
                              ├── scan
                              └── publish artifact
```

Build configuration is commonly stored as YAML or JSON in source control. Keep meaningful build logic reviewable and reproducible rather than configuring release behaviour only through the console.

```yaml
steps:
  - name: gradle:8-jdk21
    entrypoint: gradle
    args: [clean, check]
```

## Steps and Dependencies

Steps run sequentially by default. Use step identifiers and `waitFor` when work can run in parallel or has explicit dependencies. Every step should have a clear input, output, timeout, and failure meaning.

Use trusted builder images, pin important versions or immutable digests, and keep custom builders minimal. A builder image is executable supply-chain input and requires the same review and vulnerability management as application images.

## Triggers

Triggers can start builds from branches, tags, pull requests, or other source events. Define filters carefully so untrusted change requests cannot reach production credentials or deployment steps.

Separate workflows when trust differs:

- pull-request validation without privileged secrets;
- post-merge build and artifact publication;
- controlled environment promotion;
- manually approved production deployment where required.

Trigger identity, build execution identity, and deployment identity are separate concerns. Do not solve every stage with one highly privileged service account.

## Service Accounts and Permissions

Run builds with a user-specified service account where practical. Grant only the permissions required for source retrieval, logs, artifact publication, tests, or deployment.

Consider both directions of access:

- who may start or modify a build;
- what the build service account may access;
- who may impersonate that service account;
- which credentials are available to untrusted source code;
- whether a build can change its own pipeline or permissions.

Build logs and artifacts can leak credentials even when secret values are masked. Avoid shell tracing and tools that print their environment.

## Private Pools and Networking

Default builds run in Google-managed ephemeral environments. Private pools provide dedicated worker resources and more control over region, network access, machine type, and scaling.

Use private pools when builds need private network dependencies, specific capacity, data-location control, or stronger isolation. They add operational and cost decisions including worker availability, network routes, egress, DNS, and quota.

Do not expose production databases directly to arbitrary build steps. Prefer controlled test environments, narrow service interfaces, or dedicated deployment mechanisms.

## Artifacts and Provenance

Publish immutable build outputs to an artifact repository rather than rebuilding separately for each environment. Record source revision, dependency lock state, builder identity, checksums, test results, and provenance.

```text
one verified build -> immutable artifact -> development -> staging -> production
```

Scanning and provenance help assess an artifact; policy enforcement determines whether unapproved artifacts can be deployed. Retain enough metadata to trace a production version back to its source and build.

## Caching and Reproducibility

Caches improve build time but can introduce stale or poisoned state. Key caches using relevant dependency manifests, tool versions, platform, and architecture. Treat a cache miss as a supported path and periodically verify clean builds.

Pin dependencies, builders, and toolchains where reproducibility matters. Avoid relying on mutable `latest` tags or undeclared packages fetched from a developer-specific location.

## Testing and Diagnostics

Run cheap validation early and fail fast, while retaining complete reports from work that ran. Test build configuration changes through a safe trigger or non-production project before enabling production authority.

For failures, retain:

- build and step identifiers;
- source revision and trigger information;
- builder image digests and substitutions;
- logs, test reports, and artifact metadata;
- service-account and permission-denial context;
- private-pool capacity and network evidence where relevant.

## Readiness Checklist

You should be able to:

- model a build as containerised steps with explicit dependencies;
- distinguish trigger, build, and deployment identities;
- isolate untrusted changes from privileged credentials;
- decide between managed workers and private pools;
- publish immutable artifacts with traceable provenance;
- use caches without making clean builds impossible;
- diagnose step, permission, network, quota, and capacity failures.

## Official References

- [Cloud Build overview](https://cloud.google.com/build/docs/overview)
- [Build configuration file schema](https://cloud.google.com/build/docs/build-config-file-schema)
- [Cloud Build security](https://cloud.google.com/build/docs/securing-builds/overview)
- [Use a user-specified service account](https://cloud.google.com/build/docs/securing-builds/configure-user-specified-service-accounts)

Return to [Google Cloud Platform](./README.md).
