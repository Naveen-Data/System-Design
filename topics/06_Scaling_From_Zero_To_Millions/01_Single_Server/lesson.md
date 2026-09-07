# Single Server

## Problem

You're building something new — an MVP, a side project, a hackathon entry. You have no users yet, or a handful. You need *a* running system, today, without spending a week on infrastructure you don't yet need.

## Core idea

Run everything on one machine: the web server, your application code, and the database, all as processes on the same box.

A request's journey:

1. The user's browser asks DNS to resolve your domain to an IP address.
2. DNS returns the IP of your one server.
3. The browser sends the HTTP request straight to that server.
4. The web/app process handles it, and talks to the database — which is just another process on the *same* machine, usually over a local Unix socket or `localhost`, not the network.
5. The response comes back the same way.

The detail worth noticing: step 4 has no network hop. App and database are so close (same disk, same CPU, same RAM) that this is the fastest possible setup for talking to your data — you don't get that locality again until you deliberately co-locate services later.

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Everything on one box | Trivial to set up and deploy; no network latency between app and DB | One crash takes down the whole system — app, DB, everything |
| Single machine | One thing to monitor, patch, back up | Can't scale app and DB independently — if the DB needs more RAM but the app needs more CPU, you're stuck upgrading both together |
| No orchestration needed | Nothing to learn upfront — no load balancer, no replication | A traffic spike lets the app and DB fight over the same CPU/RAM, so both slow down at once instead of one absorbing the hit |

The core limitation isn't "it's slow" — a single decent server can serve real traffic. It's that the machine is a **single point of failure**, and its two very different workloads (serving requests, running queries) are forced to share resources they'd rather not.

## Where it's used

MVPs, hackathon projects, internal tools with a handful of users, and the first deploy of almost every real product that later scales — this is a legitimate starting point, not a mistake to avoid.

## Diagram

![Single server: one machine running both the app process and the database](diagrams/single-server-architecture.svg)
