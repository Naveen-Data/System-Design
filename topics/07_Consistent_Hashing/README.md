# Consistent Hashing

## Problem

Spreading data/requests across multiple servers needs a fast answer to "which server owns this key?" — and that answer needs to stay stable as servers are added or removed.

## Core idea

**Naive approach — `hash(key) % N`:** simple, O(1), works fine while N is fixed. But changing N (adding/removing a server) changes the modulus, which scrambles the result for nearly every key, not just the ones logically tied to the change — a single server addition can invalidate almost an entire cache or force a full DB reshuffle.

**The fix — consistent hashing:** map both servers and keys onto the same circular hash space (a ring). A key is owned by the first server found walking clockwise from its position. Adding/removing a server only reassigns the one arc adjacent to it — everything else on the ring is untouched.

**Virtual nodes:** hashing each physical server to many ring positions (not just one) evens out load that would otherwise be uneven by hash luck.

## Trade-offs

| | Gain (+) | Cost (−) |
|---|---|---|
| Ring-based ownership | Membership changes reshuffle only ~1/N of keys, not nearly all of them | More complex than plain `hash % N` — needs the ring structure and a clockwise-lookup |
| Graceful scaling | Nodes join/leave without a global rebalance | The `1/N` of keys that do move still have to be physically migrated — e.g. a cache miss burst while the new server's cache is empty |
| Virtual nodes | Even load distribution across physical servers | Without them, distribution can be quite uneven |
| — | — | Doesn't remove sharding's other costs — cross-node queries/transactions are just as hard as with plain sharding |

## Where it's used

Distributed caches (Memcached, Redis Cluster), distributed hash tables (Chord), CDN request routing, and DB sharding schemes needing graceful node changes (Cassandra, DynamoDB).

## Diagrams

![Hash ring: servers and keys mapped onto the same circular space; a key is owned by the next server clockwise](diagrams/hash-ring.svg)

![Adding a node: only the new server's arc gets reassigned — everything else on the ring is untouched](diagrams/adding-a-node.svg)
