# Data Centre

## Problem

Everything built so far — [load balancer](../03_Load_Balancer_Multiple_App_Servers/lesson.md), [replicas](../04_DB_Replication/lesson.md), [cache](../05_Cache/lesson.md) — still lives inside **one** data center. A [CDN](../06_CDN/lesson.md) puts static assets near users worldwide, but the actual application (dynamic requests, logins, writes — anything CDN can't cache) still all runs in that one facility. If that data center loses power, its network provider fails, or the region has an outage, the *entire* system goes down — no number of redundant machines inside it changes that, because the failure is at the level of the building, not the machine.

## Core idea

Run the whole stack in **multiple data centers**, in different geographic regions. Each region gets its own full copy: load balancer, app servers, cache, and a database (a replica of the same DB from lesson 4, now geographically distributed instead of just being in another rack).

**GeoDNS** replaces plain DNS: instead of always resolving to one place, it routes each user to their *nearest healthy* data center — the same idea as CDN edge routing (lesson 6), but now for the full application, not just static files.

The genuinely hard part is **data**, not app servers — app servers are stateless copies, trivial to duplicate anywhere. The database is not:

- One region holds the **primary** DB (accepts writes) — same primary/replica split as lesson 4, just stretched across continents instead of across racks.
- Every other region holds a **read replica**, kept in sync from that primary.
- A **read** in any region can be served entirely locally, from that region's own replica.
- A **write** from any region still has to reach the *one* primary — even a user physically closest to the EU data center has their write cross the Atlantic if the primary lives in the US.

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Multiple data centers | Survives an entire facility/region going down — the previous SPOF was "one machine," now even "one whole building" isn't fatal | Running N full copies of your infrastructure — multiplied cost and operational complexity |
| Local reads per region | Lower latency for dynamic, non-cacheable requests too (not just CDN-eligible static assets) | Writes from a non-primary region still cross the globe to reach the one primary — this doesn't fully fix write latency everywhere |
| GeoDNS routing | Automatically sends users to their nearest healthy region | Cross-region replication lag is worse than same-datacenter replication — the network between regions is slower and less reliable |

**Being honest about what this doesn't solve:** unless you take on a genuinely advanced multi-primary/active-active database design (a much bigger topic on its own), there's still exactly *one* place writes can land. Multi-region buys you read locality and disaster tolerance — it doesn't, by itself, give every region equally fast writes.

## Worked example

A company runs three data centers: US-East (holds the primary DB), EU, and Asia.

1. A user in Europe does a **read** — GeoDNS routes them to the EU data center, which serves it entirely from its local replica. Fast, no cross-region hop.
2. That same user places an **order** (a write) — even though their app server is local (EU), the write must reach the primary DB in US-East, since only the primary accepts writes. This one request crosses the Atlantic, slower than the read.
3. The US-East data center suffers a power outage. Health checks detect it, and GeoDNS reroutes *all* traffic — including US users — to EU or Asia. But if neither EU nor Asia has a promoted primary yet, **every write globally is stuck** until one of their replicas is promoted — the exact same failover mechanic from lesson 4, just now happening at data-center scale instead of single-machine scale.

## Where it's used

Large-scale global systems with real availability/latency requirements — major social platforms, global e-commerce, or any business with regulatory disaster-recovery requirements.

## Diagram

![Multi-region: GeoDNS routes users to their nearest data center; reads stay local, writes still cross to the one primary region](diagrams/multi-region.svg)
