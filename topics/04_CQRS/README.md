# CQRS (Command Query Responsibility Segregation)

## Problem

A single data model optimized for writes (normalized, validation-heavy) is often a poor fit for fast reads, and vice versa.

## Core idea

Split reads and writes into separate models: the **Command side** (normalized, validation-focused) handles writes; the **Query side** (denormalized, fast-retrieval-focused) handles reads. The two are synced via events, so the query side is eventually consistent with the command side.

## Trade-offs

Eventual consistency is acceptable here because the cost of briefly stale reads is low — unlike banking, where CP is required.

## Where it's used

Read-heavy systems with complex write validation, e.g. product catalogs with heavy search/browse traffic but controlled inventory writes.

## Diagram

![Diagram](diagram.svg)
