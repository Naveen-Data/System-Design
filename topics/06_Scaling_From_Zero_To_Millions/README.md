# Scaling From Zero To Millions

## Overview

The classic progression from an MVP running on one machine to a system that can serve millions of users. Each step below fixes exactly one bottleneck the previous step left open — resource contention, then single points of failure, then read scaling, then latency, then background-work latency, then write scaling — culminating in a system where every layer can grow independently by adding more machines.

## Subtopics

1. [Single Server](01_Single_Server/README.md) — everything on one machine; simple, but resource contention and a single point of failure.
2. [Application & DB Server Separation](02_Application_DB_Server_Separation/README.md) — split app and DB onto separate machines; fixes resource contention, not the SPOF.
3. [Load Balancer & Multiple App Servers](03_Load_Balancer_Multiple_App_Servers/README.md) — multiple app servers behind a load balancer; finally kills the app-tier SPOF, requires statelessness.
4. [DB Replication](04_DB_Replication/README.md) — primary handles writes, replicas handle reads; read scaling, but writes still bottlenecked to one primary.
5. [Cache](05_Cache/README.md) — cache-aside in front of the DB; cuts repeat reads, introduces the invalidation/staleness trade-off.
6. [CDN](06_CDN/README.md) — the same cache-aside mechanic, geographically distributed, for static assets close to users.
7. [Data Centre](07_Data_Centre/README.md) — the whole stack replicated across regions via GeoDNS; survives a facility/region outage, writes still reach one primary region.
8. [Messaging Queue](08_Messaging_Queue/README.md) — producer/consumer decoupling for slow background work; buffers spikes the same way a cache does, just for work instead of reads.
9. [DB Scaling](09_DB_Scaling/README.md) — sharding splits the data itself across machines, finally scaling writes past one primary's ceiling.
