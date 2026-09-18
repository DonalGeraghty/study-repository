---
tags:
  - platform-engineering
  - moc
---

# Platform Engineering

These guides cover application packaging, container orchestration, cloud platforms, delivery automation, caching, and messaging.

## Containers and Orchestration

- [Docker](./platform-engineering-docker.md) — images, containers, Dockerfiles, storage, networking, Compose, registries, and runtime security.
- [Kubernetes](./platform-engineering-kubernetes.md) — cluster architecture, workloads, Services, configuration, scaling, security, and operations.
- [Nginx](./platform-engineering-nginx.md) — static frontend serving, SPA fallback, caching, headers, health, and container operation.

## Data Platforms

- [MySQL](./platform-engineering-mysql.md) — relational integrity, parameterised access, transactions, indexing, connections, and recovery.

## Caching and Messaging

- [Caching](./platform-engineering-caching.md) — why caches improve latency and reduce repeated work, plus the consistency problems they introduce.
- [Redis](./platform-engineering-redis.md) — in-memory data storage commonly used for caching, sessions, counters, and messaging patterns.
- [Publish/Subscribe](./platform-engineering-pub-sub.md) — the pub/sub messaging model, topics, publishers, subscribers, and event-driven communication.
- [RabbitMQ](./platform-engineering-rabbitmq.md) — message brokering with exchanges, queues, bindings, and asynchronous consumers.
- [Apache Kafka](./platform-engineering-kafka.md) — the partitioned, replicated event log, consumer groups, offsets, and exactly-once boundaries.
- [Amazon SNS](./platform-engineering-amazon-sns.md) — AWS's managed pub/sub service and how it differs from SQS and EventBridge.
- [Amazon SQS](./platform-engineering-amazon-sqs.md) — AWS's managed message queue service for durable asynchronous work, buffering, and decoupling services.

## Cloud and Delivery

- [Cloud Platforms](./cloud/README.md) — AWS and GCP infrastructure, managed services, security, reliability, and operations.
- [Continuous Integration and Delivery](./ci-cd/README.md) — Jenkins pipelines, build infrastructure, quality gates, releases, and deployments.

## Suggested Use

Learn Docker first so images, container processes, storage, and networks are familiar before studying how Kubernetes schedules and reconciles containerised workloads. Use the caching and messaging guides to understand how services share data and communicate asynchronously, the cloud guides to record where those workloads run, and the CI/CD guides to capture how software is built, tested, and delivered.

Return to the [documentation library](../README.md).
