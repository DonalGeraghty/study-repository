# SQL

SQL is a declarative language for defining, querying, and changing relational data. You describe the required result; the database optimiser chooses an execution plan. Correct SQL therefore depends on data meaning, keys, cardinality, transaction boundaries, and the behaviour of the selected database—not syntax alone.

## Tables, Keys, and Constraints

A table represents a relation with named columns and rows. Constraints keep invalid states out of the database:

```sql
CREATE TABLE test_run (
    id          BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    suite_name  VARCHAR(200) NOT NULL,
    status      VARCHAR(20) NOT NULL
                CHECK (status IN ('passed', 'failed', 'cancelled')),
    started_at  TIMESTAMP NOT NULL,
    UNIQUE (suite_name, started_at)
);
```

Primary keys identify rows. Foreign keys enforce relationships. `NOT NULL`, `UNIQUE`, and `CHECK` constraints express invariants nearer to the data than application validation alone.

## Query Processing

A useful conceptual order for a `SELECT` is:

```text
FROM and JOIN -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT
```

This explains why a `SELECT` alias is often unavailable in `WHERE`: filtering conceptually happens earlier.

```sql
SELECT suite_name, COUNT(*) AS failed_count
FROM test_run
WHERE status = 'failed'
GROUP BY suite_name
HAVING COUNT(*) >= 3
ORDER BY failed_count DESC;
```

Without `ORDER BY`, row order is not guaranteed.

## Joins and Cardinality

Choose a join from the relationship required:

- `INNER JOIN` keeps matching rows.
- `LEFT JOIN` keeps every left row and supplies `NULL` for missing right rows.
- `CROSS JOIN` forms combinations deliberately.

Before joining, state whether each side is one-to-one, one-to-many, or many-to-many. Unexpected duplicates are often a cardinality problem, not something to hide with `DISTINCT`.

```sql
SELECT r.id, COUNT(f.id) AS failure_count
FROM test_run AS r
LEFT JOIN test_failure AS f ON f.run_id = r.id
GROUP BY r.id;
```

`COUNT(f.id)` returns zero for a run without failures; `COUNT(*)` would count the retained left row.

## Missing Values

`NULL` represents missing or unknown information and introduces three-valued logic. Use `IS NULL` and `IS NOT NULL`, not `= NULL`. Comparisons with `NULL` normally produce unknown, which a `WHERE` clause does not retain.

Use `COALESCE` only when substituting a value is semantically correct. Missing, zero, empty text, and false are not automatically equivalent.

## Aggregates and Window Functions

Aggregation collapses rows into groups. Window functions calculate across related rows while retaining individual rows:

```sql
SELECT
    suite_name,
    started_at,
    duration_ms,
    AVG(duration_ms) OVER (
        PARTITION BY suite_name
        ORDER BY started_at
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS moving_average
FROM test_run;
```

Specify ordering and frame semantics explicitly when the calculation depends on them.

## Transactions and Concurrency

A transaction groups changes into one unit of work:

```sql
BEGIN;
UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

Rollback restores the transaction when the operation cannot complete. Isolation levels balance consistency against concurrency and permit different phenomena. Keep transactions short, access resources in a consistent order, and handle deadlocks or serialization failures according to the database contract.

## Indexes and Performance

Indexes can accelerate selective lookup, joins, ordering, and uniqueness checks, but they consume storage and add write cost. Index design should follow real query predicates and ordering, not every column.

Inspect the database’s execution plan and measure with representative data. Common performance problems include:

- fetching unused columns or unbounded rows;
- missing or ineffective indexes;
- applying functions that prevent useful index access;
- repeated application queries instead of set-based work;
- incorrect cardinality estimates or stale statistics;
- long transactions and lock contention.

## Application Safety and Testing

Always bind untrusted values as parameters. String concatenation creates injection risk and can break quoting rules.

Test migrations forward and, where supported, recovery or rollback procedures. Verify constraints, transaction behaviour, permissions, indexes, and queries against the actual database engine because SQL dialects and concurrency semantics differ.

## Readiness Checklist

You should be able to:

- model identifiers, relationships, constraints, and nullability;
- predict how filters, joins, grouping, and ordering affect rows;
- distinguish aggregation from window calculations;
- define transaction boundaries and reason about concurrent access;
- interpret an execution plan before tuning a query;
- use parameters and least-privilege database accounts;
- test schema changes and queries with realistic data volumes.

## Official References

- [PostgreSQL SQL tutorial](https://www.postgresql.org/docs/current/tutorial-sql.html)
- [PostgreSQL SQL language reference](https://www.postgresql.org/docs/current/sql.html)
- [SQLite SQL language](https://www.sqlite.org/lang.html)
- [SQL Server documentation](https://learn.microsoft.com/sql/)

Return to the [Programming Languages](./README.md) guide.
