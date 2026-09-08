# DB Replication

## Problem

The database is still one machine — every read and write goes through it, capping throughput, and if it dies, everything dies with it.

## Core idea

Add replica databases holding a copy of the primary's data. The primary accepts writes only; replicas serve reads, kept in sync via replication from the primary. App servers must route writes to the primary and reads to a replica.

Replication can be async (fast writes, replicas briefly lag) or sync (no lag, but writes wait on replica acknowledgment).

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Read replicas | Read traffic scales horizontally | Writes still bottlenecked to one primary — this doesn't fix write scaling |
| Async replication | Writes stay fast | Replica lag — brief window of stale reads after a write |
| Sync replication | No lag | Every write is slower, bounded by the slowest replica |

Trap: if the primary dies, promoting a replica isn't automatic by default — something (human or failover tooling, e.g. a managed service like AWS RDS) has to detect the failure and promote a replica. Writes are unavailable until that happens.

## Where it's used

Any production database beyond small scale — MySQL/Postgres primary-replica setups, or a managed offering like an AWS RDS read replica.

## Diagram

![Primary-replica: app servers write to the primary, which replicates asynchronously to replicas that serve reads](diagrams/primary-replica.svg)
