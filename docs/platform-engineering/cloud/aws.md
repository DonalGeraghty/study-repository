# Amazon Web Services

Amazon Web Services provides infrastructure and managed services across regional data centres. Effective AWS design begins with workload requirements, failure boundaries, identity, network paths, data ownership, and operational responsibility—not a list of service names.

## Resource and Failure Boundaries

An AWS account is a strong boundary for identity, billing, quotas, and service configuration. AWS Organizations groups accounts and can apply organisation-level controls.

Regions are separate geographic areas. Availability Zones are distinct infrastructure locations within a Region. Some services are global, some regional, and some zonal; verify the scope of every resource because it affects availability, naming, recovery, and cost.

```text
organisation
└── organisational units
    ├── production account
    ├── non-production account
    └── security or shared-services account
```

Separate production from experimentation and centralise audit or security capabilities without creating one account that every workload can modify.

## Identity and Access Management

IAM evaluates identities, policies, resources, conditions, and explicit denies. Prefer temporary role credentials over long-lived access keys.

Key practices include:

- federate workforce access and require strong authentication;
- assign roles to workloads instead of distributing static keys;
- grant least privilege and narrow resource scope and conditions;
- separate deployment authority from application runtime authority;
- restrict cross-account trust explicitly;
- monitor policy changes and denied requests;
- retain emergency access with controlled, tested procedures.

An IAM permission answers whether an API action is authorised. It does not make the action safe, validate application data, or replace network and resource policies.

## Networking

A Virtual Private Cloud is a regional network boundary containing subnets. Subnets map to one Availability Zone. Route tables determine paths; security groups provide stateful resource-level filtering; network ACLs provide stateless subnet-level filtering.

```text
internet or private network
          |
 gateway, load balancer, or private connectivity
          |
      VPC route
          |
   private workload -> private service endpoint or controlled egress
```

Use private connectivity where it materially reduces exposure, but remember that private is not automatically trusted. Control egress, DNS, administrative paths, and service-to-service identity. Document every route from user to workload and workload to dependency.

## Compute and Containers

Select compute from operational needs:

| Need | Typical AWS category |
| --- | --- |
| Full host control or specialised runtime | Virtual machines |
| Managed container scheduling | Container services or managed Kubernetes |
| Event-driven short-lived execution | Serverless functions |
| Managed web or application runtime | Platform service |

Consider startup time, execution duration, scaling behaviour, networking, state, observability, deployment model, portability, and team operating capability. Stateless application processes simplify replacement and scaling; durable state belongs in a service designed for it.

## Storage and Data

Object, block, and file storage provide different semantics. Managed relational, key-value, document, cache, search, streaming, and warehouse services trade control for operational assistance.

For every data store define:

- consistency and transaction requirements;
- encryption and key ownership;
- backup, retention, restore, and deletion behaviour;
- replication and failure scope;
- access paths and least-privilege policies;
- schema or contract evolution;
- expected volume, throughput, latency, and cost.

A backup is not proven until restore is tested. Multi-AZ availability is not automatically multi-Region disaster recovery.

## Infrastructure as Code

Manage repeatable environments through reviewed infrastructure code. AWS-native templates and general infrastructure-as-code tools can model resources, but state, credentials, provider versions, and deployment ordering still require governance.

Plan changes, review destructive replacements, apply through controlled roles, detect drift, and avoid manual production changes except through documented emergency procedures. Separate reusable modules from environment-specific composition without hiding every service setting behind abstraction.

## Observability and Reliability

Collect application and platform metrics, structured logs, traces, events, configuration changes, and audit activity. Define service-level indicators around user outcomes, then alert on actionable symptoms rather than every resource fluctuation.

Design for failure by using timeouts, bounded retries with jitter, idempotency, queues where decoupling helps, health checks, load shedding, and tested recovery. Spread across Availability Zones only when every dependency and routing layer supports the intended resilience.

## Security and Cost

Use encryption in transit and at rest as part of a broader threat model. Centralise findings, patch supported compute, scan dependencies and images, protect secrets in an appropriate secret service, and log privileged activity.

Cost is an architectural signal. Tag or otherwise attribute ownership, set budgets and anomaly detection, remove unused resources, match capacity to demand, and understand data-transfer and managed-service request pricing before scale makes them dominant.

## Testing and Delivery

Validate infrastructure code, policy, configuration, and deployment behaviour before production. Use ephemeral or representative environments where possible, test permission failures, and exercise rollback and regional or zonal failure scenarios proportional to risk.

## Readiness Checklist

You should be able to:

- explain accounts, Regions, Availability Zones, and resource scope;
- design temporary-role access and evaluate least privilege;
- trace VPC routing, filtering, ingress, egress, and DNS;
- choose compute and storage from workload properties;
- deliver reviewed infrastructure code and detect drift;
- define telemetry, availability, backup, restore, and disaster recovery;
- connect architecture decisions to security responsibility and cost.

## Official References

- [AWS documentation](https://docs.aws.amazon.com/)
- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [IAM best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)

Return to the [Cloud Platforms](./README.md) guide.
