# Using zero-powers with OpenAI Codex

## Installation

```bash
git clone https://github.com/zeromicro/zero-powers.git ~/.codex/plugins/zero-powers
```

## Configuration

Add to Codex plugin config:

```json
{
  "plugins": {
    "zero-powers": {
      "enabled": true
    }
  }
}
```

## Available Skills

When working on go-zero projects, Codex loads relevant domain skills automatically:

- `rest-api` — `.api` files, handler/logic/model directories
- `rpc-service` — `.proto` files, gRPC services
- `database` — sqlx, MongoDB, Redis operations
- `resilience` — Circuit breaker, rate limiting, concurrency
- `observability` — Tracing, metrics, logging
- `deployment` — Canary, Helm, HPA, GitOps
- `event-driven` — Kafka, RabbitMQ, CQRS
- `testing` — Unit, integration, contract, benchmark
- `security` — mTLS, RBAC, OAuth, API governance
- `goctl` — Code generation commands

## Agents

- `@gozero-architect` — Architecture design before coding
- `@gozero-reviewer` — go-zero convention code review
- `@gozero-best-practices` — Production readiness validation

## goctl Commands

Codex runs goctl commands directly in the terminal. See the goctl skill for the complete command reference.
