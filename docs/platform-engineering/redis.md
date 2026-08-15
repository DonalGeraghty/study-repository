# Redis

Redis is an in-memory data store with purpose-built data structures and atomic operations. It is commonly used for caches, sessions, counters, rate limits, leaderboards, and streams. Its speed does not remove the need to design data lifetime, memory bounds, durability, and failure behaviour.

## Quick Refresh

| Data type | Useful for | Example command |
| --- | --- | --- |
| String | cached value, flag, counter | `SET`, `GET`, `INCR` |
| Hash | fields belonging to one entity | `HSET`, `HGETALL` |
| List | ordered work or recent items | `LPUSH`, `BRPOP` |
| Set | unique membership | `SADD`, `SISMEMBER` |
| Sorted set | scores, ranks, time-ordered members | `ZADD`, `ZRANGE` |
| Stream | append-only event entries and consumer groups | `XADD`, `XREADGROUP` |

Redis commands on a single key are atomic. A sequence of separate commands is not automatically atomic.

## Key and Lifetime Design

Use predictable, namespaced keys and make the ownership visible:

```text
catalogue:v2:product:481
session:v1:7f3c...
tenant:42:rate-limit:checkout:2026-08-15T10:25
```

Avoid placing secrets or personal information directly in key names because keys appear in diagnostics. Include a tenant or user scope where data isolation requires it. Version a key when its serialised representation changes incompatibly.

Set an expiration for temporary data at creation time:

```redis
SET session:v1:abc123 '{"userId":42}' EX 1800
HSET product:v1:481 name "Keyboard" priceCents 7500
EXPIRE product:v1:481 300
INCR metrics:v1:checkout-attempts
ZADD leaderboard:v1 9800 player-42
```

The hash example uses two commands, so a process failure could leave the hash without an expiry. A transaction or server-side script is appropriate when both changes must occur together.

## Cache-Aside with Redis

```python
import json


def find_user(user_id, redis_client, repository):
    key = f"user:v1:{user_id}"
    value = redis_client.get(key)

    if value is not None:
        return json.loads(value)

    user = repository.find(user_id)
    if user is not None:
        redis_client.set(key, json.dumps(user), ex=120)
    return user
```

Use a bounded client connection pool and short timeouts. If Redis is only an optimisation, catch the expected connection failure and fall back carefully; do not catch every programming error as though it were a cache miss.

## Atomic Updates and Transactions

Operations such as `INCR`, `HINCRBY`, and conditional `SET` are atomic and are preferable to read-modify-write in application code.

```redis
SET lock:invoice:730 worker-9 NX PX 10000
```

This acquires a value only if the key does not exist and gives it a bounded lifetime. A real distributed lock also needs a unique ownership token, safe conditional release, and a decision about work that outlives the lease. A single command is not a complete coordination protocol.

`MULTI`/`EXEC` queues a transaction of commands. `WATCH` can provide optimistic concurrency. Lua scripts or server-side functions can make a multi-step operation atomic, but they block Redis while running, so keep them bounded and deterministic.

## Persistence, Replication, and Eviction

Redis can persist data through snapshots, an append-only file, or both. Replicas and persistence improve recovery options but do not make every acknowledged write immune to process, host, or regional failure. Define the permitted data-loss window and test restoration.

When memory reaches its configured limit, the eviction policy determines whether Redis removes expiring keys, removes keys from a broader set, or rejects writes. A cache should have an intentional `maxmemory` and policy. A store containing durable or coordination data may need rejection rather than silent eviction.

Replication is commonly asynchronous. A failover can expose lag or lose recently acknowledged writes. Clustered Redis partitions keys into hash slots; multi-key operations then require compatible key placement and clients that understand cluster redirection.

## Redis Streams and Pub/Sub

Redis Pub/Sub delivers messages to currently connected subscribers and does not provide a durable backlog. Redis Streams retain entries and support consumer groups, pending entries, and acknowledgements. Choose between them from loss, replay, ordering, and consumer-recovery requirements.

For durable business workflows, compare Redis Streams with a dedicated broker or managed messaging service. Operational familiarity and failure semantics matter more than the shortest implementation.

## Operational Checks

- Set connection, command, and retry bounds; unlimited retries amplify outages.
- Track memory, fragmentation, evictions, expired keys, command latency, and rejected connections.
- Look for hot keys, large values, blocking commands, and unbounded collections.
- Restrict network access, require authentication, use encrypted transport where appropriate, and rotate credentials.
- Back up and restore if the data cannot be reconstructed.
- Test failover with the client and topology used by the application.

Avoid broad key scans in application request paths. Prefer `SCAN` for controlled operational iteration, while remembering that even incremental scanning consumes resources and does not provide a frozen snapshot.

## Common Failure Modes

- treating Redis as “just memory” while storing required state in it;
- omitting TTLs from cache and session keys;
- using `KEYS *` against a busy production instance;
- storing huge JSON documents when callers need one field;
- using a distributed lock without ownership-safe release;
- assuming a replica has already received the latest write;
- sharing one Redis deployment between unrelated workloads with incompatible eviction or latency needs;
- retrying non-idempotent operations without understanding whether they ran.

## Practice

Design Redis keys for a login session, a five-minute product cache, a per-user API rate limit, and a leaderboard. For each, state the data type, TTL, atomic operation, failure behaviour, and whether losing the value is acceptable.

## Official References

- [Redis data types](https://redis.io/docs/latest/develop/data-types/)
- [Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
- [Key eviction](https://redis.io/docs/latest/develop/reference/eviction/)
- [Redis Streams](https://redis.io/docs/latest/develop/data-types/streams/)

## Related Guides

- [Caching](./caching.md)
- [Publish/Subscribe](./pub-sub.md)
- [RabbitMQ](./rabbitmq.md)
- [Docker](./docker.md)

Return to [Platform Engineering](./README.md).
