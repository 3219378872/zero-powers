---
name: event-driven
description: >-
  Use when implementing event-driven architectures with go-zero — Kafka
  producers/consumers, RabbitMQ message queues, CQRS pattern (command/query
  separation), Outbox pattern for reliable event publishing, or event sourcing.
  Also trigger when designing async communication between microservices.
---

# go-zero Event-Driven

## Core Principles

### Always Follow
- **At-least-once delivery**: Design consumers to be idempotent
- **Dead letter queue**: Configure DLQ for unprocessable messages
- **Schema evolution**: Version event schemas, handle forward/backward compatibility
- **Outbox pattern**: Write events to outbox table in same transaction as business data
- **Ordering**: Use partition keys for ordered processing where needed

### Never Do
- Publish events outside the business transaction boundary (use Outbox)
- Skip idempotency in consumer handlers
- Use message queues for synchronous request-response (use gRPC)
- Ignore consumer lag monitoring

## Common Workflows

### Publish Events
1. Create outbox table in database
2. Write event to outbox in business transaction
3. Background worker publishes to Kafka/RabbitMQ
4. Details: [event-driven-patterns.md](../../references/event-driven-patterns.md)

### Consume Events
1. Implement consumer with idempotency key
2. Handle processing failures (retry, DLQ)
3. Details: [event-driven-patterns.md](../../references/event-driven-patterns.md)

## References
- [Event-Driven Patterns](../../references/event-driven-patterns.md) — Kafka, RabbitMQ, CQRS, Outbox
