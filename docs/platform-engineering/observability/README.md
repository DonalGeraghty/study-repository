---
tags:
  - platform-engineering/observability
  - moc
---

# Observability

Observability helps you understand a running system through the data it produces. Metrics show measurements over time, logs record events, and traces follow work across components. Use them together to investigate user-visible failures.

## Guides

1. [Grafana](./grafana.md) — data sources, dashboards, exploration and alerting, with a clear distinction between Grafana and its telemetry backends.
2. [Datadog](./datadog.md) — a managed observability platform, collection and instrumentation, service tags, monitors and incident investigation.

## Suggested Use

Start with Grafana to understand dashboards and the systems supplying their data, then read Datadog for an integrated managed-platform approach. Compare the operational ownership and integration choices rather than treating either as universally better. Grafana Cloud also provides a managed option.

For either tool, ask: are users affected, where does the evidence point, and is the telemetry itself complete? Relate the examples to workloads in the [Kubernetes guide](../kubernetes.md) and releases from the [CI/CD guides](../ci-cd/README.md).

Return to [Platform Engineering](../README.md).
