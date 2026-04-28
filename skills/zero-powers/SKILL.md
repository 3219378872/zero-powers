---
name: zero-powers
description: >-
  Use when building microservices with go-zero framework — REST APIs
  (handler/logic/model), gRPC services (zrpc), database operations (sqlx,
  MongoDB, Redis caching), middleware, resilience patterns (breaker, rate
  limiting), or running goctl commands. Also trigger when editing .api/.proto
  files or working under internal/handler/logic/svc/model directories. For
  specific guidance, this skill redirects to the appropriate domain skill.
---

# zero-powers — go-zero Development Plugin

This is the entry skill for the zero-powers plugin. When triggered, identify the
user's specific need and load the appropriate domain skill or reference.

## Domain Skills

- **rest-api** — REST API handlers, middleware, types, routing
- **rpc-service** — gRPC services, zrpc clients, streaming
- **database** — SQL, MongoDB, Redis, caching, transactions
- **resilience** — Circuit breakers, rate limiting, retry, load shedding
- **observability** — Tracing, metrics, structured logging, health checks
- **deployment** — Docker, Kubernetes, CI/CD
- **event-driven** — Kafka, RabbitMQ, CQRS, outbox pattern
- **testing** — Unit, integration, contract, load testing
- **security** — mTLS, RBAC, OAuth 2.0, CORS, audit logging
- **goctl** — API/RPC/model code generation commands
- **structure-review** — Project specs review via Bash-loaded `.claude/specs/`, structured pass/fail audit
- **zero-init-specs** — Interactive project specs initialization from codebase analysis

## Quick Reference

| Task | Domain Skill | Key Reference |
|------|-------------|---------------|
| New REST API | rest-api | [rest-api-patterns.md](../references/rest-api-patterns.md) |
| gRPC service | rpc-service | [rpc-patterns.md](../references/rpc-patterns.md) |
| Database operations | database | [database-patterns.md](../references/database-patterns.md) |
| Rate limiting | resilience | [resilience-patterns.md](../references/resilience-patterns.md) |
| Add logging/metrics | observability | [observability-patterns.md](../references/observability-patterns.md) |
| Docker/K8s deploy | deployment | [deployment-patterns.md](../references/deployment-patterns.md) |
| Message queue | event-driven | [event-driven-patterns.md](../references/event-driven-patterns.md) |
| Write tests | testing | [testing-patterns.md](../references/testing-patterns.md) |
| Auth/CORS | security | [security-patterns.md](../references/security-patterns.md) |
| Generate code | goctl | [goctl-commands.md](../references/goctl-commands.md) |
| Review project specs | structure-review | [.claude/specs/](../../.claude/specs/) |
| Initialize specs | zero-init-specs | [templates/specs/](../templates/specs/) |

## Shared Resources

- [Best Practices](../best-practices/overview.md) — Production hardening checklist
- [Troubleshooting](../troubleshooting/common-issues.md) — Common issues and fixes
- [Checklists](../checklists/) — Handler, RPC, middleware, migration checklists
