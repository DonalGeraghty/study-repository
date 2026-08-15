# Cloud Storage

Cloud Storage is managed object storage. It stores immutable object data and metadata inside buckets. It is suited to artifacts, media, backups, exports, data lakes, and static assets; it is not a mounted block device or a relational database.

## Buckets and Objects

A bucket has a globally unique name, location, storage configuration, access policy, and lifecycle settings. An object has a name, data, metadata, and generation.

```text
project
└── bucket
    ├── reports/2026/january.csv
    └── reports/2026/february.csv
```

In a conventional flat-namespace bucket, slashes are part of the object name rather than true directories. Hierarchical-namespace buckets add folder semantics and must be selected when creating the bucket.

Objects are replaced rather than edited in place. Generation and metageneration preconditions help prevent concurrent writers from silently overwriting one another.

## Location and Storage Class

Select a bucket location from latency, availability, data residency, service colocation, recovery design, and network cost. A multi-region is not automatically the correct choice for every workload.

Storage classes trade minimum storage duration and retrieval charges against lower at-rest cost. Choose from actual access frequency and recovery needs. Autoclass or lifecycle rules can manage transitions when access patterns justify them.

Do not use an archival class for data that must be restored frequently or under an untested time-and-cost assumption.

## Access Control

Prefer uniform bucket-level access so IAM controls access consistently and object ACLs cannot create hidden exceptions. Grant roles at the narrowest useful bucket, managed-folder, project, or higher boundary.

Common access patterns include:

- workload service accounts using client libraries;
- signed URLs for time-limited object access;
- public access prevention for buckets that must never be public;
- separate identities for readers, writers, lifecycle administration, and deletion.

A signed URL delegates a specific operation for a limited time. Protect the signing capability and keep expiry and object scope narrow.

## Encryption and Data Protection

Cloud Storage encrypts server-side data by default. Customer-managed keys add control but also create a dependency on key permissions and availability.

Protection mechanisms solve different problems:

| Mechanism | Purpose |
| --- | --- |
| Soft delete | Retain recently deleted buckets or objects for recovery |
| Object Versioning | Retain noncurrent generations after replacement or deletion |
| Retention policy | Prevent deletion before a defined age |
| Bucket Lock | Make a retention policy irreversible |
| Lifecycle rules | Transition or delete objects according to conditions |

Versioning and soft deletion increase storage consumption. Retention can prevent urgent deletion. Model legal, security, recovery, and cost requirements together.

## Uploads, Downloads, and Consistency

Use resumable uploads for large or interruption-prone transfers. Validate checksums and content type, and use preconditions when duplicate or conflicting requests matter.

Design object names for expected listing and lifecycle operations. Avoid placing sensitive information in names because names may appear in logs and operational tools.

Retry transient failures with exponential backoff and idempotent operations. A repeated create or delete needs generation conditions when accidental replacement or deletion is unacceptable.

## Events and Integrations

Object changes can drive event-based processing through supported notification and event services. Consumers should tolerate duplicate delivery, delayed processing, and an object being replaced or deleted before handling completes.

Cloud Run jobs or services can process uploaded objects. Store processing status separately when the object itself cannot represent workflow state safely.

## Operations and Cost

Monitor bytes stored, operation counts, retrieval, egress, error rates, access denials, lifecycle actions, and unexpected public exposure. Enable audit logging appropriate to the sensitivity and investigation requirements.

Major cost drivers include stored bytes, storage class, operation volume, retrieval, early deletion, replication location, and network transfer. Test lifecycle rules against sample inventories before applying broad deletion.

## Testing and Recovery

Test permissions, signed URL expiry, uploads, checksum failure, concurrent updates, lifecycle rules, retention, object restore, and regional dependencies. A backup bucket in the same administrative and credential boundary may not protect against account compromise or mistaken policy changes.

## Readiness Checklist

You should be able to:

- distinguish object storage from file and block storage;
- choose bucket location and storage class from workload requirements;
- design IAM, uniform access, signed URLs, and public access prevention;
- distinguish versioning, soft delete, retention, and lifecycle rules;
- protect concurrent writes with generations and preconditions;
- process events idempotently;
- estimate and monitor storage, operation, retrieval, and transfer cost.

## Official References

- [Cloud Storage overview](https://cloud.google.com/storage/docs/introduction)
- [Storage classes](https://cloud.google.com/storage/docs/storage-classes)
- [Access control overview](https://cloud.google.com/storage/docs/access-control)
- [Data protection and recovery](https://cloud.google.com/storage/docs/protection-backup-recovery-overview)

Return to [Google Cloud Platform](./README.md).
