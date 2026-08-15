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

## Worked Example: Reserve Stock

Let the database enforce invariants that must survive every application path:

```sql
CREATE TABLE product (
    id BIGINT PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    stock INT NOT NULL,
    CONSTRAINT product_stock_non_negative CHECK (stock >= 0)
);

CREATE TABLE reservation (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    request_id VARCHAR(64) NOT NULL UNIQUE,
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT reservation_quantity_positive CHECK (quantity > 0),
    CONSTRAINT reservation_product_fk
        FOREIGN KEY (product_id) REFERENCES product(id)
);
```

A transaction can lock the product row, verify stock, and make the related changes together:

```sql
START TRANSACTION;

SELECT stock
FROM product
WHERE id = ?
FOR UPDATE;

UPDATE product
SET stock = stock - ?
WHERE id = ? AND stock >= ?;

INSERT INTO reservation (request_id, product_id, quantity)
VALUES (?, ?, ?);

COMMIT;
```

The application must check that the conditional update changed one row. The unique `request_id` makes a repeated request detectable, and the constraints protect the database even if another code path forgets validation. Roll back on every failed path.

Concurrent transactions can deadlock even when both are individually correct. Access rows in a consistent order and retry a transaction only when its full operation is safe to repeat.

## Index Example

An index should follow a demonstrated query shape:

```sql
CREATE INDEX reservation_product_created
    ON reservation (product_id, created_at);

EXPLAIN
SELECT id, quantity, created_at
FROM reservation
WHERE product_id = ?
  AND created_at >= ?
ORDER BY created_at DESC;
```

The leading index columns should support filtering and ordering used by the query. More indexes are not free: they consume storage and add work to inserts and updates. Inspect the actual execution plan and representative data distribution.

## Common Failure Modes

- relying only on application validation instead of durable constraints;
- using floating-point columns for exact money values;
- holding a transaction open during an HTTP call;
- adding indexes without measuring read and write impact;
- using a connection pool with no acquisition timeout or upper bound;
- retrying a partially completed non-idempotent operation;
- creating backups without testing a restore.

## Project Connections

`tododos-express-api` uses the Node.js `mysql` and `promise-mysql` packages behind an Express API.

## Related Guides

- [SQL](../programming/languages/sql.md)
- [Express](../programming/frameworks/express.md)
- [REST APIs](../quality-engineering/rest-api.md)
- [Docker](./docker.md)

Return to [Platform Engineering](./README.md).
