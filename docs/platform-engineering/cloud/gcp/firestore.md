# Firestore

Firestore is a managed document database. Data is stored as documents containing fields, organised into collections. It supports indexed queries, transactions, real-time listeners, offline-capable client SDKs, and server-side libraries.

## Documents and Collections

A document has an identifier and nested fields. Collections contain documents; documents can contain subcollections.

```text
users/{userId}
  name: "Ava"
  plan: "pro"

users/{userId}/orders/{orderId}
  total: 42.00
  status: "paid"
```

Document paths are part of the data model. Choose stable identifiers and avoid encoding mutable business meaning into paths.

Firestore is not a relational database. There are no server-side joins in ordinary queries. Model data around known read and write access patterns, sometimes duplicating derived values deliberately. Duplication requires a consistency and repair strategy.

## Data Modelling

Decide whether related data should be:

- embedded in one document for atomic reads and updates;
- placed in a subcollection for unbounded child data;
- stored in a top-level collection for broad querying;
- duplicated to serve a required read efficiently.

Avoid documents that grow without bound, such as one array containing every event or member. Large frequently updated documents can create contention and cost.

Name fields consistently and define how schema changes are introduced. A schemaless database still has an application schema; it is simply enforced through code, validation, security rules, tests, and migration processes.

## Queries and Indexes

Firestore queries are served by indexes. Basic indexes are maintained automatically, while compound query patterns may require composite indexes.

Design queries before finalising the data model:

```text
required result
-> collection scope
-> filters and ordering
-> index
-> expected document reads
```

Queries do not scan arbitrary unindexed data like an in-memory collection. Inequality, ordering, array membership, and multiple filters have defined combinations and index requirements.

Use cursors for scalable pagination. Offset-based pagination can still incur work for skipped results and becomes unstable when data changes.

## Transactions and Batched Writes

A transaction reads documents and applies writes atomically. The transaction function may run more than once when concurrent changes require a retry, so it must not perform external side effects.

A batched write groups writes atomically without reading first. Use it when the new values do not depend on current database state.

Keep transaction sets small and avoid a single frequently updated document as a global counter or lock. Use distributed designs or supported aggregation patterns when write contention is expected.

## Client Access and Security Rules

Mobile and web clients can connect directly through Firebase SDKs. Security Rules then become a primary authorisation boundary and must validate identity, resource ownership, allowed fields, and state transitions.

Server client libraries use IAM and bypass Firestore Security Rules. A trusted server must implement application-level authorisation itself.

```text
mobile or web client -> Firebase authentication + Security Rules -> Firestore
trusted server       -> IAM service identity + application checks -> Firestore
```

Test rules with authorised, unauthorised, missing, malformed, and cross-tenant requests. A query must be compatible with what the rules can prove; rules are not post-query filters.

## Real-Time and Offline Behaviour

Listeners receive an initial snapshot and later changes. UI code must handle loading, empty, error, reconnect, duplicate rendering, and listener cleanup.

Supported client SDKs can cache data and queue writes offline. This improves resilience but introduces stale reads, later synchronisation, and conflict behaviour. Make user feedback clear when data is pending or may be out of date.

## Reliability and Recovery

Select database location from user latency, application placement, availability needs, and data residency. Keep Cloud Run and other compute close enough to avoid unnecessary latency and network cost.

Define export, restore, point-in-time recovery, retention, and deletion procedures according to the selected Firestore edition and enabled capabilities. Test recovery into an isolated environment and verify application compatibility with restored data.

## Cost and Performance

Cost depends on document reads, writes, deletes, storage, index storage, listeners, backup features, and network transfer. A small query result can still be expensive if repeated frequently across many clients.

Monitor:

- request volume and latency;
- denied and failed operations;
- index creation and storage;
- hot documents and contention;
- listener count and reconnect patterns;
- document and query read amplification;
- backup and transfer cost.

## Testing

Use the emulator for rapid local tests where appropriate, but retain integration coverage against the selected production edition and configuration. Test queries and indexes, transactions under contention, rules, offline behaviour, schema migration, retry safety, and restore.

## Readiness Checklist

You should be able to:

- model documents, collections, subcollections, and stable paths;
- design data around known queries without unbounded documents;
- explain indexes, cursors, transactions, and batched writes;
- distinguish Security Rules from server-side IAM access;
- design listeners and offline behaviour explicitly;
- identify hot-document and read-amplification risks;
- test authorisation, contention, migration, and recovery.

## Official References

- [Firestore overview](https://cloud.google.com/firestore/docs/overview)
- [Firestore data model](https://cloud.google.com/firestore/docs/data-model)
- [Queries and indexes](https://cloud.google.com/firestore/docs/query-data/indexing)
- [Transactions and batched writes](https://cloud.google.com/firestore/docs/manage-data/transactions)
- [Firestore Security Rules](https://firebase.google.com/docs/firestore/security/get-started)

Return to the [Google Cloud Platform](./README.md) guide.
