# Google Cloud Platform

Google Cloud Platform provides infrastructure and managed services organised around resource hierarchy, projects, identities, regions, and zones. Sound GCP design starts from workload boundaries, trust, data, reliability, and operations before selecting individual products.

## Service Guides

- [Cloud Run](./cloud-run.md) — managed container services and jobs, revisions, scaling, identity, networking, and delivery.
- [Cloud Build](./cloud-build.md) — containerised build steps, triggers, artifacts, service accounts, and supply-chain security.
- [Cloud Storage](./cloud-storage.md) — buckets, objects, locations, storage classes, lifecycle rules, access, and recovery.
- [Firestore](./firestore.md) — document modelling, queries, indexes, transactions, security, and operational trade-offs.
- [Identity and Access Management](./iam.md) — principals, roles, policies, inheritance, service accounts, and least privilege.

## Resource Hierarchy

Resources inherit selected policies through a hierarchy:

```text
organisation
└── folders
    ├── production projects
    ├── non-production projects
    └── security or shared-service projects
```

A project is a core boundary for APIs, IAM policy, quotas, billing attribution, and resource lifecycle. Folders group projects by environment, business unit, or policy need. Organisation policies constrain supported configurations across the hierarchy.

Use separate projects to reduce blast radius and clarify ownership. Avoid one permanent project containing unrelated production and experimental resources.

## Regions and Zones

Regions are geographic areas; zones are deployment areas within a region. Services may be global, regional, zonal, or multi-regional. Scope determines failure exposure, latency, placement, naming, and recovery.

Select location from user latency, data residency, service availability, dependency placement, and recovery needs. Multi-zone deployment helps only when all critical paths and data dependencies tolerate a zone failure.

## Identity and Access Management

IAM binds principals to roles on resources. Policy inheritance can broaden access, while deny and organisation constraints can restrict it.

Prefer:

- federated workforce identity instead of unmanaged cloud-only users;
- attached workload identities or short-lived federation instead of service-account keys;
- predefined or carefully scoped custom roles;
- separate deployment and runtime service accounts;
- least privilege at the narrowest useful resource level;
- audited impersonation for privileged workflows;
- controlled emergency access with tested recovery.

Service accounts represent workloads, not people. Protect who may attach, impersonate, or mint credentials for them.

See the dedicated [IAM guide](./iam.md) for policy evaluation, role design, conditions, service-account use, and access review.

## Networking

Virtual Private Cloud networks are global resources with regional subnets. Routes, firewall policies, load balancers, DNS, service networking, and hybrid connectivity define traffic paths.

```text
user or private network
          |
 load balancing or private connectivity
          |
 VPC firewall and route
          |
 workload -> controlled service access and egress
```

Shared VPC can centralise network administration while service projects own workloads. This improves separation only when responsibilities, IAM, quotas, and troubleshooting procedures are explicit.

Private addressing does not replace authentication or authorisation. Control ingress, egress, DNS, administrative access, and service-to-service identity.

## Compute and Containers

GCP offers virtual machines, managed instance groups, managed containers, Kubernetes, serverless functions, and application platforms. Choose according to:

- runtime and host control;
- startup, duration, and scaling characteristics;
- networking and state requirements;
- portability and deployment model;
- observability and failure handling;
- team operational capability;
- predictable and variable cost.

Keep durable state outside replaceable compute. Define resource requests, limits, scaling bounds, and shutdown behaviour.

See [Cloud Run](./cloud-run.md) for a managed container platform suited to request-driven services and run-to-completion jobs.

## Storage and Data

Object storage, persistent disks, file services, relational databases, globally distributed databases, warehouses, caches, queues, and streaming systems expose different consistency and operating models.

For each store define classification, schema, access, encryption, retention, backup, restore, replication, deletion, performance, and cost. Test restore procedures and confirm whether recovery protects against regional failure, logical corruption, operator error, and credential compromise.

See [Cloud Storage](./cloud-storage.md) for object storage and [Firestore](./firestore.md) for a managed document database.

## Infrastructure as Code

Represent projects, APIs, IAM, networks, services, and policies as reviewed code. Protect infrastructure state, pin provider behaviour, preview changes, and inspect plans for replacement or privilege expansion.

Use reusable modules for stable organisational patterns while keeping service-specific behaviour visible. Detect drift and route exceptional manual changes back into code.

## Observability and Reliability

Combine application logs, metrics, traces, error reporting, audit logs, configuration events, and service health. Define service-level indicators from user outcomes and attach alerts to actions an operator can take.

Use timeouts, idempotency, bounded retries, queues, health checks, progressive delivery, and graceful degradation. Document dependencies and practise recovery rather than assuming a managed service removes all operational responsibility.

## Security and Cost

Apply defence in depth with IAM, network policy, encryption, secrets management, vulnerability management, audit logging, and data controls. Enable only required APIs and protect project-level administrative actions.

Attribute cost with projects, labels, billing exports, budgets, and ownership. Rightsize capacity, expire temporary resources, understand network egress, and evaluate the operational saving of managed services against request and storage pricing.

## Testing and Delivery

Validate infrastructure code, policies, images, permissions, and configuration in CI. Exercise deployments, rollback, secret rotation, quota failure, zone disruption, and restore procedures according to business impact. Use representative environments without copying sensitive production data carelessly.

See [Cloud Build](./cloud-build.md) for managed build execution, triggers, artifacts, and software-supply-chain controls.

## Readiness Checklist

You should be able to:

- explain organisations, folders, projects, regions, zones, and policy inheritance;
- design workforce and workload identity without long-lived keys;
- trace VPC, firewall, ingress, egress, DNS, and hybrid paths;
- choose compute and data services from workload characteristics;
- manage infrastructure and IAM as reviewed code;
- define telemetry, reliability, backup, restore, and disaster recovery;
- connect service selection to shared responsibility and cost.

## Official References

- [Google Cloud documentation](https://cloud.google.com/docs)
- [Google Cloud Architecture Framework](https://cloud.google.com/architecture/framework)
- [Enterprise foundations blueprint](https://cloud.google.com/architecture/security-foundations)
- [IAM documentation](https://cloud.google.com/iam/docs)

Return to the [Cloud Platforms](../README.md) guide.
