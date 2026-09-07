# Microservices & Saga Pattern

## Problem

Monoliths couple scaling and deployment across all features — a bug fix in one module means redeploying and rescaling everything. Microservices split into independently deployable services, each owning its own database — but that breaks cross-service ACID transactions.

## Core idea

With separate databases, there's no free distributed transaction. The **Saga pattern** replaces one ACID transaction with a sequence of local transactions, each with a **compensating action** to undo it if a later step fails.

- **Choreography** — event-driven, decentralized, each service reacts to events from others. Hard to trace the overall flow.
- **Orchestration** — a central coordinator tells each service what to do next. Easier to debug, but more coupled to the coordinator.

## Trade-offs

Independent deployability and scaling vs. losing atomic cross-service consistency — paid back via compensating actions and eventual consistency.

## Where it's used

E-commerce order flow (reserve inventory → charge payment → ship) is the canonical Saga example.

## Diagram

![Diagram](diagram.svg)
