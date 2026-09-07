# Strangler Fig Pattern

## Problem

Rewriting a monolith into microservices all at once is high-risk — a single big-bang cutover with no gradual validation.

## Core idea

Migrate piece by piece using path-based routing at an L7 gateway: migrated functionality routes to new services, everything else still routes to the monolith. Over time, the monolith's surface area shrinks until nothing routes to it — like a strangler fig vine gradually replacing its host tree.

## Trade-offs

Slower migration timeline in exchange for low-risk, incremental cutover with rollback at the routing layer.

## Where it's used

Any large legacy-to-microservices migration; depends on [L7 load balancers](../01_Network_Protocols/README.md) for content-aware routing.

## Diagram

![Diagram](diagram.svg)
