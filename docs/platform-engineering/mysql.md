# MySQL

MySQL is a relational database management system. Applications communicate using SQL, but reliable use also requires schema design, constraints, transactions, indexes, connection management, backup, and operational ownership.

## Application Boundary

```text
HTTP request -> validation -> service -> parameterised SQL -> transaction -> response
```

Never construct SQL by concatenating untrusted values. Parameterisation protects values, while table names, column names, and sort directions require allow-listing when they are dynamic.

## Data Integrity

- Use primary keys, foreign keys, unique constraints, nullability, and checks to enforce durable invariants.
- Select column types from the domain, including precision, ranges, time-zone handling, and text encoding.
- Group related changes in transactions and define what concurrent updates should do.
- Add indexes from observed query patterns and inspect execution plans.
- Apply schema migrations as versioned, reviewed delivery steps.

## Connections and Failure

Use a bounded connection pool, release connections in every path, and configure acquisition, query, and network timeouts. A successful connection does not prove that queries are fast, permissions are minimal, replicas are current, or storage can be recovered.

Test constraints and transactions against a representative MySQL version. Define backup, restore, retention, encryption, patching, and credential-rotation procedures before data becomes important.

## Project Connections

`tododos-express-api` uses the Node.js `mysql` and `promise-mysql` packages behind an Express API.

## Related Guides

- [SQL](../programming/languages/sql.md)
- [Express](../programming/frameworks/express.md)
- [REST APIs](../quality-engineering/rest-api.md)
- [Docker](./docker.md)

Return to [Platform Engineering](./README.md).
