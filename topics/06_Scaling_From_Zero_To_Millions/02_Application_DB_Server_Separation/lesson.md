# Application & DB Server Separation

## Problem

On a [single server](../01_Single_Server/lesson.md), the app and the database were forced to share one machine's CPU and RAM — a spike in one starves the other, and you can't give the database more memory without also paying for more app-server capacity you didn't need.

## Core idea

Move the database onto its own machine. The app server now talks to the database over the network instead of a local socket.

A request's journey:

1. DNS resolves the domain to the **app server's** IP (the DB server has no public IP — nothing external ever talks to it directly).
2. The app server handles the HTTP request.
3. To fetch or store data, the app server opens a network connection to the DB server (e.g. `postgres://db-host:5432`) instead of `localhost`.
4. The DB server runs the query and sends rows back over that same connection.
5. The app server builds the response and returns it to the user.

The one thing this costs you: step 3 is now a network round-trip (even on a fast internal network, that's real latency — microseconds to low milliseconds) where before it was a local call. You're trading a bit of per-request speed for independence between the two machines.

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Two machines | App and DB no longer fight over the same CPU/RAM | Every query now crosses the network — added latency vs. a local socket |
| Independent scaling | Give the DB more RAM, or the app more CPU cores, without touching the other | Two machines to provision, patch, and monitor instead of one |
| DB has no public IP | Smaller attack surface — only the app server can reach it | The app server is now a single point of failure for reaching the DB (if it's down, nothing can query the DB either) |

Notice what this step does *not* fix: there's still exactly one app server and one DB server. A crash of either one still takes down the whole system — you've separated the resource contention, not the single point of failure. That's the next problem to solve (load balancers + multiple app servers).

## Where it's used

The default "two-tier" shape almost every web app grows into right after the single-server stage — a web/app tier and a separate database tier, each scaled and tuned on its own.

## Diagram

![Two-tier architecture: app server and DB server as separate machines, talking over the network](diagrams/two-tier-architecture.svg)
