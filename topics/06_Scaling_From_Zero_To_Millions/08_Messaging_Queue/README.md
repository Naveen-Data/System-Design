# Messaging Queue

## Problem

Some work (sending emails, transcoding video, generating reports) is slow and isn't needed before the user gets a response. Doing it inline makes the user wait for the slowest step, and a spike in that work ties up app servers that should be free for other requests.

## Core idea

A message queue lets a producer (e.g. the app server) drop a message describing work to be done and move on immediately. A separate consumer (a worker, often several in parallel) pulls messages off the queue and does the actual work at its own pace, independent of the request that created it.

## Trade-offs

| | Gain | Cost |
|---|---|---|
| Producer/consumer split | Fast, consistent user-facing response times regardless of background work speed | Work becomes eventually-consistent, not immediate — a user could see a stale state right after a "success" response |
| Independent scaling | Add worker instances to drain the queue faster, without touching app servers | A new piece of infrastructure (RabbitMQ, Kafka, SQS) to run and monitor |
| The queue as a buffer | Absorbs traffic spikes — jobs queue up, workers drain them at a sustainable rate | Failed jobs need explicit handling — retries, dead-letter queues |

Same "buffer against spikes" idea as a cache — just absorbing spikes in background work instead of reads.

Trap: most queues guarantee "at-least-once" delivery, not "exactly-once" — a worker might process the same message twice. Work should be idempotent (safe to repeat), not assumed to run exactly once.

## Where it's used

Sending emails/notifications, processing uploaded media, generating reports, order-processing pipelines. Real systems: RabbitMQ, Apache Kafka, AWS SQS.

## Diagram

![Producer publishes a message and responds immediately; workers pull from the queue and do the actual work independently](diagrams/producer-queue-consumer.svg)
