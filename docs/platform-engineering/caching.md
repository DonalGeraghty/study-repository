# Caching

Caching stores data in a faster temporary location so repeated work can be avoided. A cache can reduce latency and load, but it introduces a second copy of data whose freshness, capacity, and failure behaviour must be designed deliberately.

## Quick Refresh

| Term | Meaning |
| --- | --- |
| Cache hit | The requested value is present and can be returned |
| Cache miss | The value must be read from or calculated by the source of truth |
| TTL | The time after which an entry expires |
| Eviction | Removal of entries to enforce a memory or storage limit |
| Invalidation | Explicit removal or replacement when source data changes |
| Hit ratio | Hits divided by total lookups; useful only alongside latency and correctness |
| Stampede | Many callers regenerate the same expired value at once |

The central question is not “can this be cached?” It is “how stale may this value be, and what happens when the cache is wrong or unavailable?”

## Where Caches Appear

```text
browser -> CDN or proxy -> application cache -> database or external service
```

- A **browser or HTTP cache** reuses responses according to HTTP caching rules.
- A **CDN** stores content near users and reduces origin traffic.
- An **in-process cache** is fast but private to one process and disappears with it.
- A **distributed cache** such as Redis is shared by application instances but adds a network dependency.
- A **database buffer cache** avoids repeated disk reads and is normally managed by the database.

Each layer has different ownership and invalidation rules. Adding several cache layers can make it difficult to know which copy produced a stale response.

## Cache-Aside Example

In cache-aside, the application reads the cache first and fills it after a miss:

```python
import json

PRODUCT_TTL_SECONDS = 300


def get_product(product_id, cache, repository):
    key = f"product:v1:{product_id}"
    cached = cache.get(key)

    if cached is not None:
        return json.loads(cached)

    product = repository.find(product_id)
    if product is None:
        return None

    cache.set(key, json.dumps(product), ex=PRODUCT_TTL_SECONDS)
    return product
```

The cache key includes a schema version so an incompatible representation can be rolled out without interpreting old values. The source of truth is still the repository. If the cache is unavailable, the application may choose to read the repository directly, subject to a timeout and protection against overloading it.

When the product changes, either delete `product:v1:<id>` after the committed write or update it with the new representation. Deleting is often simpler because the next read repopulates from the source of truth.

## Read and Write Strategies

| Strategy | Behaviour | Main trade-off |
| --- | --- | --- |
| Cache-aside | Application loads missing values | Simple and common; first request is a miss |
| Read-through | Cache implementation loads missing values | Central policy; tighter cache/source integration |
| Write-through | Write cache and source synchronously | Fresh reads; greater write latency and failure complexity |
| Write-behind | Cache accepts writes and persists later | Fast writes; data-loss and ordering risk |
| Refresh-ahead | Refresh popular values before expiry | Fewer misses; may refresh unused or already changed data |

Choose from consistency and failure requirements rather than from performance alone.

## Expiration and Invalidation

TTL limits how long an entry can remain stale, but it does not make the value correct during that period. Select TTLs from the business tolerance:

- a product image may tolerate hours;
- a catalogue price may tolerate seconds or require explicit invalidation;
- an authorisation decision may be unsafe to cache at all without reliable revocation.

Add small random variation to high-volume TTLs so thousands of entries do not expire simultaneously. Negative caching can briefly store “not found” results to protect a source from repeated misses, but the TTL must be short enough that newly created data becomes visible.

## Stampedes and Hot Keys

After a popular key expires, many callers may all load the source. Common protections include:

- a per-key lock or single-flight request so one caller refreshes;
- stale-while-revalidate, where a bounded stale value is returned during refresh;
- early refresh for frequently used data;
- TTL jitter;
- request coalescing and source-side rate limits.

A hot key can overload one cache shard even when the total cluster has capacity. Split the work only when the access pattern permits it, replicate reads where supported, or redesign the request so every caller does not require the same key.

## Failure and Observability

Decide whether the cache is an optimisation or a required store. If it is an optimisation, a failure may fall back to the source; that fallback still needs timeouts, concurrency limits, and capacity. If cached sessions or coordination data are required for correctness, treating failure as an automatic cache miss can be dangerous.

Monitor:

- hit and miss counts by useful operation, not by unbounded raw key;
- cache and source latency;
- evictions, memory use, connection errors, and timeouts;
- load placed on the source after cache failure;
- stale-data and invalidation incidents;
- the distribution of key sizes and request frequency.

A high hit ratio is not success if stale data is harming users or one miss takes several seconds.

## Common Failure Modes

- caching without defining the source of truth;
- using one TTL for data with different freshness requirements;
- constructing keys without tenant or authorisation scope;
- caching errors indefinitely;
- allowing unbounded key cardinality or value size;
- issuing a cache request for every item in a loop instead of batching;
- assuming deletion and a database write are one atomic operation;
- letting a cache outage become a database outage through unrestricted fallback.

## Practice

For a product endpoint, decide how you would cache the product details, current stock, price, and a personalised discount. They do not necessarily share a TTL or even belong in the same cached object.

Explain:

1. the source of truth for each value;
2. the acceptable staleness;
3. the key and invalidation event;
4. behaviour during cache failure;
5. the measurements that would show whether caching helped.

## Related Guides

- [Redis](./redis.md)
- [Nginx](./nginx.md)
- [REST APIs and HTTP Caching](../quality-engineering/rest-api.md)
- [MySQL](./mysql.md)

Return to [Platform Engineering](./README.md).
