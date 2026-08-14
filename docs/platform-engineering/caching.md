# Caching

Caching is the practice of storing frequently used or expensive-to-compute data in a faster temporary location so future requests can be served more quickly. It is commonly used to reduce database load, lower latency, improve application responsiveness, and reduce repeated work.

A cache sits between the application and a slower source of truth. When the requested data is already cached, the application can return it immediately; when it is missing, the application retrieves the data from the original source and may store the result for later requests.

Caching introduces important design questions around expiration, invalidation, consistency, cache misses, and what should happen when the cache is unavailable. Common implementations include in-memory application caches, HTTP/CDN caching, and distributed caches such as Redis.

Return to [Platform Engineering](./README.md).
