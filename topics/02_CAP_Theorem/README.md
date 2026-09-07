# CAP Theorem

## Problem

Once data is replicated across multiple machines, a network partition between them is inevitable eventually — the system must decide how to behave when nodes can't talk to each other.

## Core idea

During a partition, a system must choose between **Consistency** (every read gets the latest write, but the system may refuse to answer if it can't guarantee that) and **Availability** (always answers, but may return stale data). You can't have both at once during a partition — only ever CP or AP, chosen per domain.

## Trade-offs

- **CP** (e.g. banking, inventory counts): refuse or block rather than serve wrong data.
- **AP** (e.g. social feeds, likes/views counters): always respond, tolerate staleness.

## Where it's used

Banking/payment systems lean CP. Social feeds, DNS, shopping cart availability lean AP.

## Diagram

![Diagram](diagram.svg)
