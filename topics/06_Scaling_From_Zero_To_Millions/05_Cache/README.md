# Cache

## Problem

Read replicas spread reads across machines, but every read is still a real DB query. Hot, barely-changing data (a popular product page, a profile) gets queried identically thousands of times, burning DB capacity on repeat answers.

## Core idea

Put a cache (Redis/Memcached) between app servers and the DB — the cache-aside pattern:

1. Check the cache first for the key.
2. Hit — return immediately, no DB involved.
3. Miss — query the DB, store the result in the cache (with a TTL), then return it.

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Cache hits | Much faster reads; DB never sees that query | New infra component to run and monitor |
| Fewer DB reads | Frees DB capacity for writes and uncached reads | Cache invalidation — stale data after the underlying row changes |
| TTL-based expiry | No invalidation code needed | Can serve stale data for the full TTL window |
| Explicit invalidation on write | Correct immediately after a write | Every write path must remember to invalidate the right key(s) |

Trap: if the cache goes down or is cold, traffic that would've hit it floods straight to the DB — a "cache stampede" that can overwhelm a DB sized for cache-reduced load.

Same underlying trade-off as DB replication lag, one layer up: an answer that's correct-as-of-a-moment-ago, traded for speed.

## Where it's used

Nearly everywhere with real read traffic — hot DB rows, API response caching, computed aggregates (leaderboards, view counts), and the external session store that app-server statelessness requires.

## Diagram

![Cache-aside: check cache first; on a hit return immediately, on a miss query the DB and populate the cache](diagrams/cache-aside.svg)
