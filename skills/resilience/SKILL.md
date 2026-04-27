---
name: resilience
description: >-
  Use when implementing resilience patterns in go-zero — circuit breaker,
  rate limiting, load shedding, optimistic/pessimistic/CAS locking,
  distributed locks, idempotency, Saga distributed transactions.
  Also trigger when dealing with concurrent writes, shared state,
  or protecting services from cascading failures.
---

# go-zero Resilience

## Core Principles

### Always Follow
- **Circuit breaking**: Use `breaker.Do` or `breaker.DoWithAcceptable` for all external calls
- **Rate limiting**: Apply `limit.PeriodLimit` or token bucket at service boundaries
- **Concurrency safety**: Use CAS/optimistic/pessimistic locking for concurrent writes
- **Idempotency**: Design write operations to be idempotent with unique keys
- **Graceful degradation**: Return cached or partial results when dependencies fail

### Never Do
- Use read-modify-write without locking (causes Lost Update)
- Call external services without circuit breaker protection
- Skip rate limiting on public-facing endpoints
- Assume distributed operations will succeed atomically (use Saga)

## Common Workflows

### Protect External Call
1. Wrap with `breaker.Do` or `breaker.DoWithAcceptable`
2. Define fallback behavior on circuit open
3. Details: [resilience-patterns.md](../../references/resilience-patterns.md)

### Handle Concurrent Writes
1. Choose locking strategy: CAS (single field), Optimistic (version), Pessimistic (row lock)
2. Implement with transaction
3. Details: [concurrency-patterns.md](../../references/concurrency-patterns.md)

## References
- [Resilience Patterns](../../references/resilience-patterns.md) — Circuit breaker, rate limiting, load shedding
- [Concurrency Patterns](../../references/concurrency-patterns.md) — Locking, distributed locks, idempotency, Saga
