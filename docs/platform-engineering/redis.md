# Redis

Redis is an in-memory data store commonly used for caching, fast key-value storage, session data, counters, rate limiting, and lightweight messaging. Because it primarily works from memory, it can serve data much faster than a traditional disk-backed database for many workloads.

Redis supports more than simple strings. Its data structures include hashes, lists, sets, sorted sets, streams, and other specialised types, which makes it useful for a wide range of application patterns.

Although Redis is often introduced as a cache, it can also be used as a primary data store in some designs. When using it for caching, applications still need to think about expiration, eviction, persistence, replication, and what happens when cached data becomes stale.

Return to [Platform Engineering](./README.md).
