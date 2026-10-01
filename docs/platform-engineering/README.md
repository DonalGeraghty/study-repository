---
tags:
  - platform-engineering
  - moc
---

# Platform Engineering

These guides cover application packaging, container orchestration, cloud platforms, delivery automation, observability, caching, and messaging.

## Containers and Orchestration

- [Docker](./docker.md) — images, containers, Dockerfiles, storage, networking, Compose, registries, and runtime security.
- [Kubernetes](./kubernetes.md) — cluster architecture, workloads, Services, configuration, scaling, security, and operations.
- [Nginx](./nginx.md) — static frontend serving, SPA fallback, caching, headers, health, and container operation.

## Data Platforms

- [MySQL](./mysql.md) — relational integrity, parameterised access, transactions, indexing, connections, and recovery.

## Caching and Messaging

- [Caching](./caching.md) — why caches improve latency and reduce repeated work, plus the consistency problems they introduce.
- [Redis](./redis.md) — in-memory data storage commonly used for caching, sessions, counters, and messaging patterns.
- [Publish/Subscribe](./pub-sub.md) — the pub/sub messaging model, topics, publishers, subscribers, and event-driven communication.
- [RabbitMQ](./rabbitmq.md) — message brokering with exchanges, queues, bindings, and asynchronous consumers.
- [Apache Kafka](./kafka.md) — the partitioned, replicated event log, consumer groups, offsets, and exactly-once boundaries.
- [Amazon SNS](./amazon-sns.md) — AWS's managed pub/sub service and how it differs from SQS and EventBridge.
- [Amazon SQS](./amazon-sqs.md) — AWS's managed message queue service for durable asynchronous work, buffering, and decoupling services.

## Observability

- [Observability guides](./observability/README.md) — how metrics, logs and traces support operational investigation.
- [Grafana](./observability/grafana.md) — data sources, dashboards, exploration and alerting.
- [Datadog](./observability/datadog.md) — collection, instrumentation, service tags, monitors and incident investigation.

## Infrastructure as Code

- [Terraform](./terraform.md) — declarative infrastructure, providers, state, plans, modules, environment isolation, and safe changes.

## Cloud and Delivery

- [Cloud Platforms](./cloud/README.md) — AWS and GCP infrastructure, managed services, security, reliability, and operations.
- [Continuous Integration and Delivery](./ci-cd/README.md) — Jenkins pipelines, build infrastructure, quality gates, releases, and deployments.

## Developer Portals

- [Backstage](./backstage.md) — software ownership, catalogs, templates, documentation and integrated developer workflows.

## Suggested Use

Learn Docker first so images, container processes, storage, and networks are familiar before studying how Kubernetes schedules and reconciles containerised workloads. Use the caching and messaging guides to understand how services share data and communicate asynchronously, the cloud guides to record where those workloads run, and the CI/CD guides to capture how software is built, tested, and delivered.

After learning a cloud platform's resource and identity model, use Terraform to practise provisioning and changing that infrastructure through reviewed code. Connect its plan/apply workflow to the CI/CD guides for team delivery.

Use the observability guides to connect deployments with runtime evidence: identify the affected service, inspect its signals and verify whether a mitigation improved user outcomes.

Return to the [documentation library](../README.md).
