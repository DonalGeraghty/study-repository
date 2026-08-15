# Cloud Platforms

This section covers public-cloud infrastructure, managed services, identity, networking, data, reliability, security, operations, and cost. Provider vocabulary differs, but sound architecture begins with workload requirements and explicit failure and trust boundaries.

## Guides

- [Amazon Web Services](./aws.md) — AWS infrastructure, identity, networking, compute, storage, observability, and delivery services.
- [Google Cloud Platform](./gcp/README.md) — GCP projects, identity, networking, compute, storage, observability, and delivery services.

## Comparison Dimensions

- Account, organisation, project, and resource hierarchy
- Identity and access management
- Virtual networking and connectivity
- Compute, containers, serverless services, and storage
- Infrastructure as code and environment management
- Security, observability, reliability, and cost control

## Shared Principles

- Separate production and non-production with strong resource and identity boundaries.
- Prefer short-lived workload identity to static access keys.
- Manage infrastructure, policy, and configuration through reviewed code.
- Design around regional, zonal, dependency, and operator failures.
- Test restore and disaster-recovery procedures.
- Attribute ownership and cost before resources proliferate.

Avoid translating architectures service-for-service between providers. Compare capabilities from the workload’s consistency, latency, availability, security, operational, and cost requirements.

Return to [Platform Engineering](../README.md).
