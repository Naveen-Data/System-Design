# Load Balancer & Multiple App Servers

## Problem

One app server is a single point of failure — if it crashes or traffic exceeds its capacity, there's no second machine to take over.

## Core idea

Put a load balancer (LB) in front of multiple identical app servers. DNS resolves to the LB's IP, not any app server's. The LB picks a server per request (round robin / least connections) and health-checks all of them, routing around any that stop responding.

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Multiple app servers | No single app server is a SPOF; horizontal scaling as traffic grows | App servers must be stateless — session data goes in a shared store, not server memory |
| Health checks | LB automatically routes around a crashed server | One more hop adds slight latency; health-check tuning itself takes care |

New trap introduced: the load balancer is now the single point of failure — if it's down, no app server is reachable. Production setups run LBs redundantly (pair, or a managed LB service).

## Where it's used

Essentially every production web system beyond hobby scale — AWS ALB, Nginx, HAProxy, or a cloud provider's managed load balancer.

## Diagrams

![Normal flow: load balancer distributing requests across three app servers, all sharing one DB](diagrams/normal-flow.svg)

![Failover: the LB's health check catches App Server 2 down and stops routing to it](diagrams/failover.svg)
