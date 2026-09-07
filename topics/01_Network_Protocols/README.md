# Network Protocols (Foundation Layer)

## Problem

Everything above this layer assumes two machines can find each other, exchange bytes reliably and privately, and do it fast. That's built up in layers, not automatic.

## Core idea

- **TCP handshake** (SYN → SYN-ACK → ACK) — establishes reliability and syncs sequence numbers, not security.
- **TLS handshake** — asymmetric crypto once to exchange a shared secret, then symmetric crypto for the rest of the session. Certificates prove server identity.
- **HTTP vs HTTPS** — same protocol; HTTPS is HTTP wrapped in TLS.
- **DNS resolution** — resolver → root → TLD → authoritative server. TTL controls caching (higher TTL = lower latency, slower to react to change).
- **HTTP/2** — multiplexes many requests over one TCP connection, avoiding repeated handshake cost.
- **WebSockets** — start as HTTP, `101 Switching Protocols` upgrades to a persistent full-duplex connection.
- **L4 vs L7 load balancers** — L4 routes on IP/port only; L7 terminates TLS to read HTTP content before routing.

## Trade-offs

High DNS TTL trades change-propagation speed for lower average latency. L7 LBs trade extra CPU/latency (TLS termination) for content-aware routing.

## Where it's used

L7 path-based routing underlies the [Strangler Fig pattern](../05_Strangler_Fig_Pattern/README.md). WebSockets power chat/live-feed apps.

## Diagram

![Diagram](diagram.svg)
