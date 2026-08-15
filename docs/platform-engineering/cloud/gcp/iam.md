# Identity and Access Management

Google Cloud Identity and Access Management controls which principals may perform which actions on which resources. IAM is an authorisation system; authentication establishes the principal, while IAM policies determine permitted access.

## Core Model

The central relationship is:

```text
principal + role + resource + optional condition -> access decision
```

- A **principal** is an authenticated human, group, service account, workload, or federated identity.
- A **permission** allows one API operation on a resource type.
- A **role** groups permissions.
- An **allow policy** binds principals to roles on a resource.
- A **deny policy** blocks supported permissions and overrides an allow.
- A **condition** limits a binding by attributes such as resource, time, or request context.

IAM does not normally grant access directly through individual permissions; permissions are packaged in roles.

## Resource Hierarchy and Inheritance

Allow policies can be attached at the organisation, folder, project, or supported resource level. Descendants inherit access granted by ancestors.

```text
organisation policy
      v
folder policy
      v
project policy
      v
service resource policy
```

A project-level grant can reach many resources, including ones created later. Grant at the narrowest stable boundary that matches the responsibility. Do not assume a resource’s local policy shows all effective access; inherited bindings also matter.

Deny policies and organisation constraints serve different purposes. Deny policies prevent specified IAM permissions; organisation policies constrain supported resource configurations.

## Principals and Groups

Assign workforce access through managed groups rather than many individual bindings. Groups express job responsibilities and simplify joining, leaving, and review.

Use federated workforce identity where appropriate so the organisation’s identity provider remains authoritative. Require strong authentication and control privileged group membership separately.

Avoid broad special principals such as all authenticated users unless public or organisation-wide access is explicitly intended and reviewed.

## Roles

Google Cloud provides:

- **basic roles**, which are broad and generally unsuitable for fine-grained production access;
- **predefined roles**, maintained for service responsibilities;
- **custom roles**, created when predefined roles do not match a stable least-privilege need.

Start from a predefined role. Use permission usage and denied-request evidence to reduce access carefully. Custom roles require ownership, compatibility monitoring, and a process for new permissions.

Do not create one organisation-wide custom administrator role that accumulates unrelated capabilities.

## Service Accounts and Workload Identity

A service account is a workload identity. Separate identities by application, environment, and responsibility so permissions and audit events remain meaningful.

There are distinct permissions for:

- using a service account as a workload identity;
- attaching it to a resource;
- impersonating it to obtain short-lived credentials;
- administering the service account itself;
- creating or managing long-lived keys.

Prefer attached identities, service-account impersonation, or workload identity federation over downloaded keys. If a key is unavoidable, restrict creation, inventory its owner and use, rotate it, monitor it, and remove it promptly.

Never place a service-account key in source control, a container image, a mobile application, or a general-purpose shared drive.

## Conditions and Deny Policies

IAM Conditions can narrow a grant by time or supported resource and request attributes. Keep conditions readable, tested, and documented; a complex expression that no one can review safely becomes an operational risk.

Deny policies override allow policies for supported permissions. Use them for high-value guardrails where a future broad allow must not grant a dangerous action. Test exception principals and recovery paths before applying a wide deny.

## Privilege Boundaries

Separate responsibilities such as:

- organisation and policy administration;
- security monitoring and audit access;
- network administration;
- project creation and billing;
- application deployment;
- application runtime;
- data administration;
- emergency access.

Control who may change IAM as carefully as the resources IAM protects. Permission to set a policy, impersonate a privileged service account, deploy code under an identity, or modify a trusted build can all create indirect privilege escalation.

## Reviews and Troubleshooting

Use policy analysis, access recommendations, audit logs, and asset inventory to review effective access. Review group membership, inherited grants, service-account impersonation, keys, unused permissions, and externally accessible principals.

For a denied request, identify:

1. the authenticated principal;
2. the exact permission required;
3. the target resource and its hierarchy;
4. applicable allow bindings and conditions;
5. applicable deny policies or organisation constraints;
6. whether credentials and tokens reflect recent changes.

Do not fix every denial by granting a broad project role.

## Infrastructure as Code and Testing

Manage IAM through reviewed code where practical. Avoid authoritative policy resources that accidentally replace unrelated bindings unless that ownership is intentional.

Test positive and negative access: prove that required work succeeds and forbidden work fails. Validate policy changes in a non-production hierarchy, inspect the plan for membership removal or privilege expansion, and preserve an emergency recovery path.

## Readiness Checklist

You should be able to:

- distinguish authentication, principals, permissions, roles, and policies;
- trace inherited access through the resource hierarchy;
- select predefined roles and govern custom roles;
- use groups for people and service accounts for workloads;
- avoid long-lived keys through impersonation or federation;
- explain conditions, deny precedence, and indirect privilege escalation;
- diagnose a denial without applying an unnecessarily broad grant.

## Official References

- [IAM overview](https://cloud.google.com/iam/docs/overview)
- [Resource hierarchy for access control](https://cloud.google.com/iam/docs/resource-hierarchy-access-control)
- [IAM roles](https://cloud.google.com/iam/docs/understanding-roles)
- [Service accounts](https://cloud.google.com/iam/docs/service-account-overview)
- [Deny policies](https://cloud.google.com/iam/docs/deny-overview)

Return to [Google Cloud Platform](./README.md).
