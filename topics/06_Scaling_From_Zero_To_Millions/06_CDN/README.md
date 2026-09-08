# CDN

## Problem

A cache cuts DB queries, but doesn't fix physical distance — a user far from your servers still pays real network latency on every request, and static assets (images, JS/CSS bundles) get re-fetched from origin by every user worldwide.

## Core idea

A CDN is a globally distributed set of edge servers ("points of presence") caching content close to users. Same cache-aside mechanic as a regular cache, just geographically distributed and fronting the whole origin instead of just the DB:

1. Request routes to the nearest edge (via DNS/anycast).
2. Edge hit — return immediately, no origin involved.
3. Edge miss — fetch from origin once, cache it (TTL), return it. Nearby future requests then hit.

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Edge locations near users | Latency drops dramatically for distant users | Another piece of infra to configure, paid per-bandwidth |
| Origin absorbs far fewer requests | Static traffic barely touches origin | Only helps content that's the same for every user — dynamic/personalized/write requests (login, checkout, a feed) still hit origin |
| TTL-based edge caching | Same simple model as a regular cache | Same staleness cost, multiplied across every edge — an update reaches all edges only after TTL expiry or an explicit purge |

## Where it's used

Nearly every production site/app for static assets — images, videos, JS/CSS bundles, downloads. Providers: Cloudflare, Akamai, AWS CloudFront, Fastly.

## Diagrams

![Global distribution: users routed to their nearest edge location; edges only reach the origin on a miss](diagrams/global-distribution.svg)

![Edge hit/miss: same cache-aside mechanic, applied at a nearby edge instead of a local cache](diagrams/edge-hit-miss.svg)
