# zero-powers Plugin Design

## Overview

将现有的 `zero-powers` 单一 skill 重构为 `zero-powers` 插件，拆分为 10 个领域独立 skill + 3 个 go-zero 专用 agent，面向 Claude Code 和 Codex 双平台。

## Plugin Metadata

- **Name**: `zero-powers`
- **Description**: go-zero microservices development plugin — 10 domain skills covering REST API, gRPC, database, resilience, observability, deployment, event-driven, testing, security, and goctl commands, plus 3 specialized agents for architecture, code review, and production readiness
- **License**: MIT
- **Platforms**: Claude Code (primary), Codex (future)

## Directory Structure

```
zero-powers/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── .codex/
│   └── INSTALL.md
├── skills/                              # 10 domain skills
│   ├── rest-api/SKILL.md
│   ├── rpc-service/SKILL.md
│   ├── database/SKILL.md
│   ├── resilience/SKILL.md
│   ├── observability/SKILL.md
│   ├── deployment/SKILL.md
│   ├── event-driven/SKILL.md
│   ├── testing/SKILL.md
│   ├── security/SKILL.md
│   └── goctl/SKILL.md
├── agents/                              # 3 go-zero specialized agents
│   ├── gozero-reviewer.md
│   ├── gozero-architect.md
│   └── gozero-best-practices.md
├── references/                          # 12 shared reference files
│   ├── rest-api-patterns.md
│   ├── rpc-patterns.md
│   ├── database-patterns.md
│   ├── resilience-patterns.md
│   ├── concurrency-patterns.md
│   ├── observability-patterns.md
│   ├── deployment-patterns.md
│   ├── event-driven-patterns.md
│   ├── testing-patterns.md
│   ├── security-patterns.md
│   ├── api-governance-patterns.md
│   └── goctl-commands.md
├── checklists/                          # 6 shared checklists
│   ├── README.md
│   ├── new-api-definition-checklist.md
│   ├── new-handler-checklist.md
│   ├── new-rpc-service-checklist.md
│   ├── new-middleware-checklist.md
│   ├── db-migration-checklist.md
│   └── production-readiness-checklist.md
├── best-practices/
│   └── overview.md
├── troubleshooting/
│   └── common-issues.md
├── getting-started/                     # Multi-platform guides
│   ├── README.md
│   ├── claude-code-guide.md
│   ├── codex-guide.md                   # New
│   ├── copilot-guide.md
│   ├── cursor-guide.md
│   └── windsurf-guide.md
├── templates/
│   └── project-CLAUDE.md
├── examples/
│   ├── README.md
│   ├── verify-tutorial.sh
│   └── demo-project/
├── README.md
├── README_CN.md
└── LICENSE
```

## Skills Design

### Skill → Reference Mapping

```
skills/rest-api/SKILL.md          → references/rest-api-patterns.md
skills/rpc-service/SKILL.md       → references/rpc-patterns.md
skills/database/SKILL.md          → references/database-patterns.md
skills/resilience/SKILL.md        → references/resilience-patterns.md
                                    + concurrency-patterns.md
skills/observability/SKILL.md     → references/observability-patterns.md
skills/deployment/SKILL.md        → references/deployment-patterns.md
skills/event-driven/SKILL.md      → references/event-driven-patterns.md
skills/testing/SKILL.md           → references/testing-patterns.md
skills/security/SKILL.md          → references/security-patterns.md
                                    + api-governance-patterns.md
skills/goctl/SKILL.md             → references/goctl-commands.md
```

### Skill Template

Each SKILL.md follows this structure:

```markdown
---
name: <skill-name>
description: >-
  Precise trigger description with keywords, file patterns,
  and contexts for automatic loading
---

# <Skill Title>

## Core Principles

### Always Follow
- Key rules specific to this domain

### Never Do
- Anti-patterns to avoid

## Common Workflows

### Workflow 1
1. Step one
2. Step two
3. Reference: [pattern-guide.md](../../references/<pattern>.md)

## Checklists
- Relevant checklist: [checklist.md](../../checklists/<checklist>.md)
```

### Trigger Descriptions

| Skill | Trigger Keywords & Contexts |
|-------|---------------------------|
| `rest-api` | `.api` files, handler/logic/model directories, HTTP endpoints, CRUD, middleware, error handling |
| `rpc-service` | `.proto` files, gRPC services, zrpc, service discovery, load balancing |
| `database` | sqlx, MongoDB, Redis caching, `goctl model` generation, database operations |
| `resilience` | Circuit breaker, rate limiting, load shedding, CAS/optimistic/pessimistic locking, distributed locks, idempotency, Saga |
| `observability` | Tracing, metrics, logging, health checks, SLO, monitoring |
| `deployment` | Canary, Helm, HPA, GitOps, database migrations, CI/CD |
| `event-driven` | Kafka, RabbitMQ, CQRS, Outbox pattern, message queues |
| `testing` | Unit tests, integration tests, contract tests, load tests, benchmarks, chaos testing |
| `security` | mTLS, RBAC, OAuth, CORS, audit logging, API versioning, error codes, pagination, OpenAPI |
| `goctl` | `goctl` commands, code generation, config/deployment template generation |

## Agents Design

### Agent Files

```
agents/
├── gozero-reviewer.md
├── gozero-architect.md
└── gozero-best-practices.md
```

### Agent Details

#### gozero-architect
- **Position in workflow**: brainstorming → writing-plans phase
- **Purpose**: Design go-zero microservice architecture before coding
- **Responsibilities**:
  - Service decomposition (API vs RPC selection)
  - Data storage strategy (database per service, caching)
  - Middleware planning
  - Generate `.api` / `.proto` file examples
  - Follow three-layer architecture (Handler → Logic → Model)
  - Reference: `references/` for specific patterns

#### gozero-reviewer
- **Position in workflow**: code-review phase (after implementation)
- **Purpose**: Review go-zero code against framework conventions
- **Responsibilities**:
  - Three-layer separation integrity (no business logic in handlers)
  - Context propagation through all layers
  - goctl-generated code not modified
  - Concurrency safety (no read-modify-write without locking)
  - Structured errors (`httpx.Error` for HTTP, `status.Error` for gRPC)
  - No hardcoded configuration values
  - No discarded errors (especially `LastInsertId()`, `RowsAffected()`)
  - Reference: `checklists/`, `best-practices/overview.md`

#### gozero-best-practices
- **Position in workflow**: post-implementation verification
- **Purpose**: Production readiness validation
- **Responsibilities**:
  - Configuration validation (`conf.MustLoad` + ServiceContext)
  - Error handling completeness
  - Logging and tracing setup
  - Security hardening (mTLS, RBAC, CORS, rate limiting)
  - Run `checklists/production-readiness-checklist.md`
  - Run `checklists/db-migration-checklist.md` when applicable
  - Reference: `best-practices/overview.md`, `troubleshooting/common-issues.md`

### Integration with Superpowers Flow

```
brainstorming → writing-plans → executing-plans → gozero-best-practices
     ↑              ↑                                   ↓
gozero-architect    |                             gozero-reviewer
                    +-----------------------------------+
                                                  ↓
                                          finishing-branch
```

## Shared Resources

### References (12 files, ~8900 lines)

Shared across all skills. Each skill references specific files via relative paths (`../../references/<file>.md`).

### Checklists (6 files)

- `new-api-definition-checklist.md` — API definition pre-flight
- `new-handler-checklist.md` — Handler implementation verification
- `new-rpc-service-checklist.md` — RPC service pre-flight
- `new-middleware-checklist.md` — Middleware validation
- `db-migration-checklist.md` — Database migration safety
- `production-readiness-checklist.md` — Go-live verification

### Best Practices & Troubleshooting

- `best-practices/overview.md` — Production deployment and code review guidelines
- `troubleshooting/common-issues.md` — Debugging errors and configuration issues

## Items to Remove

- `skill-patterns/` — Advanced skill examples (deprecated, not included in plugin)
- `.obsidian/` — Obsidian workspace configuration
- `.git/` — Old repository history (new plugin gets fresh init)
- `.gitignore` — Replaced with plugin-appropriate version

## Items to Create

- `.claude-plugin/plugin.json` — Plugin metadata with name, version, author, repository, keywords
- `.claude-plugin/marketplace.json` — Marketplace registration
- `.codex/INSTALL.md` — Codex installation guide
- `agents/gozero-architect.md` — Architecture design agent
- `agents/gozero-reviewer.md` — Code review agent
- `agents/gozero-best-practices.md` — Production readiness agent
- `getting-started/codex-guide.md` — New Codex platform guide
- 10 `skills/*/SKILL.md` — Split from original SKILL.md

## Items to Update

- `README.md` — Rewrite as plugin documentation
- `README_CN.md` — Rewrite as plugin documentation (Chinese)
- `SKILL.md` — Removed (replaced by skills/*/SKILL.md)
