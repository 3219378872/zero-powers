# zero-powers

go-zero microservices development plugin for Claude Code and Codex — domain skills + specialized agents.

## Installation

```bash
# Claude Code
claude plugins install zero-powers@claude-plugins-official

# Manual (project-level)
git clone https://github.com/zeromicro/zero-powers.git .claude/plugins/zero-powers

# Manual (user-level, all projects)
git clone https://github.com/zeromicro/zero-powers.git ~/.claude/plugins/zero-powers
```

## What's Included

### 12 Domain Skills

| Skill | Description |
|-------|-------------|
| `rest-api` | REST APIs — `.api` files, handler/logic/model, HTTP endpoints, middleware |
| `rpc-service` | gRPC services — `.proto` files, zrpc, service discovery, load balancing |
| `database` | Database — sqlx, MongoDB, Redis caching, model generation |
| `resilience` | Resilience — circuit breaker, rate limiting, locking, idempotency, Saga |
| `observability` | Observability — tracing, metrics, logging, health checks, SLO |
| `deployment` | Deployment — Canary, Helm, HPA, GitOps, DB migrations |
| `event-driven` | Event-driven — Kafka, RabbitMQ, CQRS, Outbox pattern |
| `testing` | Testing — unit, integration, contract, load, benchmarks, chaos |
| `security` | Security — mTLS, RBAC, OAuth, CORS, audit, API governance |
| `goctl` | Code generation — `goctl api go`, `goctl rpc protoc`, `goctl model`, etc. |
| `structure-review` | Project specs review — dynamic injection of `.claude/specs/`, structured pass/fail audit |
| `zero-init-specs` | Specs initialization — scans codebase for project-specific conventions, generates spec files |

### 3 Specialized Agents

| Agent | Use When |
|-------|----------|
| `@gozero-architect` | Designing microservice architecture before coding |
| `@gozero-reviewer` | Reviewing code for go-zero convention compliance |
| `@gozero-best-practices` | Validating production readiness |

### Shared Resources

- 12 pattern reference guides
- 6 development checklists
- Best practices and troubleshooting guides
- Multi-platform getting-started guides (Claude Code, Codex, Copilot, Cursor, Windsurf)
- Project templates and demo examples

## Usage

Skills load automatically when working with go-zero files (`.api`, `.proto`, go-zero `go.mod`). Agents are invoked manually:

```
@gozero-architect Design a user management microservice
@gozero-reviewer Review the current changes
@gozero-best-practices Validate production readiness
```

## License

MIT — same as go-zero framework.
