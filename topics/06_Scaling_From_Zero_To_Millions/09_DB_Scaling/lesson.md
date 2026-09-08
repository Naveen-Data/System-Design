# DB Scaling

## Problem

Every scaling technique so far — [replication](../04_DB_Replication/lesson.md), [multi-region](../07_Data_Centre/lesson.md) — scaled **reads**. Writes have been stuck on a single primary this entire time. As write volume grows, that one machine eventually hits a ceiling: you can add more RAM, faster disks, more CPU cores ("vertical scaling"), but at some point there's no bigger machine to buy, or it's prohibitively expensive.

## Core idea

The only way past one machine's ceiling is to split the **data itself** across multiple database machines — not copies (that's replication), but different *slices* of the data. This is **sharding** (horizontal partitioning):

1. Pick a **shard key** — a field to split on, commonly `user_id`.
2. Use a function of that key to decide which shard owns a given row — e.g. `hash(user_id) % number_of_shards`, or a range split (`user_id` 1–1,000,000 → shard A, 1,000,001–2,000,000 → shard B).
3. Each shard is a genuinely separate database machine — and can even have its own primary + replicas from lesson 4. Sharding and replication aren't alternatives, they combine: shard for write scaling, replicate each shard for read scaling and redundancy.
4. A routing layer (or the app server itself) computes the shard key on every query and sends it to the right shard.

Writes for different users now land on entirely different physical machines — write capacity scales with the *number of shards*, not the ceiling of one.

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Sharding | Write throughput scales horizontally — add more shards as writes grow | Cross-shard queries ("all orders across all users") now have to hit every shard and merge results — much harder than one query on one DB |
| Smaller data per shard | Smaller indexes, less contention, often faster even per-query | Cross-shard transactions/joins lose simple single-machine ACID guarantees |
| — | — | Choosing the shard key is a big, hard-to-reverse decision — an uneven key creates "hot shards" that get disproportionate traffic, and moving data between shards later (resharding) means physically relocating huge amounts of data |
| — | — | N database machines to operate instead of one (or one primary + replicas) — schema migrations, backups, and monitoring all multiply by the shard count |

**The trap worth internalizing:** sharding fixes a real, hard ceiling, but it trades away the thing that made a single database simple — one place to query, one place with strong transactional guarantees. It should be reached for when write throughput or data volume has actually outgrown vertical scaling, not by default.

## Worked example

A social platform has grown to 500 million users; its single primary, even with read replicas, can no longer keep up with the write rate for new posts, likes, and comments.

1. They shard the users/posts tables by `hash(user_id) % 100` — 100 shards.
2. User #12345 posts something. The app computes `hash(12345) % 100 = 45` and sends the write directly to shard 45's primary — a different physical machine than user #67890's write, which might land on shard 12.
3. Write throughput is now spread across 100 independent machines instead of bottlenecked on one.
4. But a query like "show globally trending posts across all users" now has to query all 100 shards and merge/rank the combined results — a much bigger engineering problem than it would've been against one database.

This closes the loop on the whole "scaling from zero to millions" arc: it started with everything on [one machine](../01_Single_Server/lesson.md), and ends here with the exact same idea — split it across more machines — applied to the one piece that was still a single point: the write path itself.

## Where it's used

Any system whose write throughput or data volume has genuinely outgrown one machine — large-scale social platforms, e-commerce at scale, any system with a huge, ever-growing dataset. Sharding by `user_id` is extremely common in practice (several large social platforms have published engineering posts about exactly this scheme).

## Diagram

![Sharding: a write for a given user routes, by a hash of the shard key, to exactly one of several independent shard databases](diagrams/sharding.svg)
