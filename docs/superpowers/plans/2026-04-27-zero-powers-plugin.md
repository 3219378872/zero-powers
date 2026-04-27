# zero-powers Plugin Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将 `zero-skills` 单一 skill 重构为 `zero-powers` 插件（10 个 domain skill + 3 个 go-zero agent）

**Architecture:** 复制并重组现有 zero-skills 文件到插件结构，创建新的 skill SKILL.md 和 agent 文件。共享资源（references/checklists/best-practices/etc.）从原 skill 复制，保持路径一致。

**Tech Stack:** Shell scripting, Markdown, YAML frontmatter, JSON

**Source:** `~/.claude/skills/zero-skills/`

---

### Task 1: Initialize directory structure and git repo

**Files:**
- Create: `zero-powers/` 完整目录树

- [ ] **Step 1: Create all directories**

```bash
cd /home/bt/projects/skill/zero-powers
mkdir -p .claude-plugin
mkdir -p .codex
mkdir -p skills/rest-api
mkdir -p skills/rpc-service
mkdir -p skills/database
mkdir -p skills/resilience
mkdir -p skills/observability
mkdir -p skills/deployment
mkdir -p skills/event-driven
mkdir -p skills/testing
mkdir -p skills/security
mkdir -p skills/goctl
mkdir -p agents
mkdir -p references
mkdir -p checklists
mkdir -p best-practices
mkdir -p troubleshooting
mkdir -p getting-started
mkdir -p templates
mkdir -p examples/demo-project
```

- [ ] **Step 2: Initialize git repo**

```bash
cd /home/bt/projects/skill/zero-powers
git init
```

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "chore: initialize zero-powers plugin directory structure"
```

---

### Task 2: Create plugin metadata files

**Files:**
- Create: `.claude-plugin/plugin.json`
- Create: `.claude-plugin/marketplace.json`
- Create: `LICENSE`

- [ ] **Step 1: Write plugin.json**

```json
{
  "name": "zero-powers",
  "description": "go-zero microservices development plugin — 10 domain skills (REST API, gRPC, database, resilience, observability, deployment, event-driven, testing, security, goctl) plus 3 specialized agents for architecture, code review, and production readiness",
  "version": "0.1.0",
  "author": {
    "name": "go-zero Community",
    "email": "go-zero@go-zero.dev"
  },
  "homepage": "https://github.com/zeromicro/zero-powers",
  "repository": "https://github.com/zeromicro/zero-powers",
  "license": "MIT",
  "keywords": [
    "go-zero",
    "microservices",
    "golang",
    "grpc",
    "rest-api",
    "code-generation",
    "skills",
    "agents",
    "best-practices"
  ]
}
```

- [ ] **Step 2: Write marketplace.json**

```json
{
  "name": "zero-powers-dev",
  "description": "Development marketplace for zero-powers go-zero microservices plugin",
  "owner": {
    "name": "go-zero Community",
    "email": "go-zero@go-zero.dev"
  },
  "plugins": [
    {
      "name": "zero-powers",
      "description": "go-zero microservices development plugin — domain skills + specialized agents",
      "version": "0.1.0",
      "source": "./",
      "author": {
        "name": "go-zero Community",
        "email": "go-zero@go-zero.dev"
      }
    }
  ]
}
```

- [ ] **Step 3: Copy LICENSE from zero-skills**

```bash
cp /home/bt/.claude/skills/zero-skills/SKILL.md /tmp/check-license.md
# LICENSE is MIT, same as go-zero framework
```

- [ ] **Step 4: Write LICENSE**

```text
MIT License

Copyright (c) 2026 go-zero Community

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

- [ ] **Step 5: Write .gitignore**

```gitignore
.obsidian/
.claude/
.DS_Store
*.log
```

- [ ] **Step 6: Write .codex/INSTALL.md**

```markdown
# Installing zero-powers for OpenAI Codex

## Prerequisites
- Codex CLI or Codex IDE integration
- Git

## Installation

```bash
# Clone as Codex plugin
git clone https://github.com/zeromicro/zero-powers.git ~/.codex/plugins/zero-powers
```

## Configuration

Add to your Codex plugin configuration:

```json
{
  "plugins": {
    "zero-powers": {
      "enabled": true,
      "skills": ["all"],
      "agents": ["all"]
    }
  }
}
```

## Activation

After installation, Codex will automatically load the appropriate go-zero domain skill when working with:
- `.api` files (REST API definitions)
- `.proto` files (gRPC service definitions)
- `internal/handler/`, `internal/logic/`, `internal/svc/` directories
- go-zero `go.mod` dependencies by zeromicro

Agents are invoked manually: `@gozero-architect`, `@gozero-reviewer`, `@gozero-best-practices`.

## Verification

Ask Codex: "What go-zero skills are available?" to confirm successful installation.
```

- [ ] **Step 7: Commit**

```bash
git add .claude-plugin/ .codex/ .gitignore LICENSE
git commit -m "feat: add plugin metadata, Codex config, license, and gitignore"
```

---

### Task 3: Copy shared references (12 files)

**Files:**
- Copy: `~/.claude/skills/zero-skills/references/*.md` → `references/`

- [ ] **Step 1: Copy all reference files**

```bash
cp /home/bt/.claude/skills/zero-skills/references/rest-api-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/rpc-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/database-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/resilience-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/concurrency-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/observability-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/deployment-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/event-driven-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/testing-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/security-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/api-governance-patterns.md references/
cp /home/bt/.claude/skills/zero-skills/references/goctl-commands.md references/
```

- [ ] **Step 2: Verify file count**

```bash
ls references/*.md | wc -l
# Expected: 12
```

- [ ] **Step 3: Commit**

```bash
git add references/
git commit -m "feat: add 12 shared reference pattern guides"
```

---

### Task 4: Copy checklists, best-practices, troubleshooting

**Files:**
- Copy: `~/.claude/skills/zero-skills/checklists/*.md` → `checklists/`
- Copy: `~/.claude/skills/zero-skills/best-practices/overview.md` → `best-practices/`
- Copy: `~/.claude/skills/zero-skills/troubleshooting/common-issues.md` → `troubleshooting/`

- [ ] **Step 1: Copy all shared resource files**

```bash
cp /home/bt/.claude/skills/zero-skills/checklists/*.md checklists/
cp /home/bt/.claude/skills/zero-skills/best-practices/overview.md best-practices/
cp /home/bt/.claude/skills/zero-skills/troubleshooting/common-issues.md troubleshooting/
```

- [ ] **Step 2: Verify**

```bash
ls checklists/*.md | wc -l     # Expected: 7 (README + 6 checklists)
ls best-practices/*.md | wc -l # Expected: 1
ls troubleshooting/*.md | wc -l # Expected: 1
```

- [ ] **Step 3: Commit**

```bash
git add checklists/ best-practices/ troubleshooting/
git commit -m "feat: add shared checklists, best-practices, and troubleshooting"
```

---

### Task 5: Copy and update getting-started guides

**Files:**
- Copy: `~/.claude/skills/zero-skills/getting-started/*.md` → `getting-started/`
- Create: `getting-started/codex-guide.md`

- [ ] **Step 1: Copy existing platform guides**

```bash
cp /home/bt/.claude/skills/zero-skills/getting-started/README.md getting-started/
cp /home/bt/.claude/skills/zero-skills/getting-started/claude-code-guide.md getting-started/
cp /home/bt/.claude/skills/zero-skills/getting-started/copilot-guide.md getting-started/
cp /home/bt/.claude/skills/zero-skills/getting-started/cursor-guide.md getting-started/
cp /home/bt/.claude/skills/zero-skills/getting-started/windsurf-guide.md getting-started/
```

- [ ] **Step 2: Write codex-guide.md**

```markdown
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
```

- [ ] **Step 3: Commit**

```bash
git add getting-started/
git commit -m "feat: add multi-platform getting-started guides including Codex"
```

---

### Task 6: Copy templates and examples

**Files:**
- Copy: `~/.claude/skills/zero-skills/templates/project-CLAUDE.md` → `templates/`
- Copy: `~/.claude/skills/zero-skills/examples/` → `examples/`

- [ ] **Step 1: Copy templates and examples**

```bash
cp /home/bt/.claude/skills/zero-skills/templates/project-CLAUDE.md templates/
cp /home/bt/.claude/skills/zero-skills/examples/README.md examples/
cp /home/bt/.claude/skills/zero-skills/examples/verify-tutorial.sh examples/
cp -r /home/bt/.claude/skills/zero-skills/examples/demo-project examples/
```

- [ ] **Step 2: Verify**

```bash
ls templates/*.md | wc -l      # Expected: 1
ls examples/README.md           # Expected: exists
ls examples/verify-tutorial.sh  # Expected: exists
ls examples/demo-project/       # Expected: directory
```

- [ ] **Step 3: Commit**

```bash
git add templates/ examples/
git commit -m "feat: add project templates and demo examples"
```

---

### Task 7: Create skills/rest-api/SKILL.md

**Files:**
- Create: `skills/rest-api/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: rest-api
description: >-
  Use when building REST APIs with go-zero — editing .api files, implementing
  handler/logic/model layers, defining HTTP endpoints with CRUD operations,
  adding middleware, handling errors with httpx.Error.
  Also trigger when working in internal/handler/, internal/logic/, internal/types/
  directories or creating new API services with goctl api go.
---

# go-zero REST API

## Core Principles

### Always Follow
- **Three-layer separation**: Handler (routing/validation) → Logic (business) → Model (data), never mix
- **Context propagation**: Pass `ctx context.Context` through all layers for tracing and cancellation
- **Type safety**: Define request/response types in `.api` files, generate with goctl
- **Structured errors**: Use `httpx.Error(w, err)` for HTTP error responses
- **Middleware layering**: Auth → Validation → Business logic

### Never Do
- Put business logic in handlers
- Hard-code configuration values (use `conf.MustLoad` + ServiceContext)
- Mix layers (handler doing database calls)
- Discard errors with `_`

## Common Workflows

### New REST API Service
1. Define API spec in `.api` file
2. Generate: `goctl api go -api xxx.api -dir .`
3. Implement logic in `internal/logic/`
4. Details: [rest-api-patterns.md](../../references/rest-api-patterns.md)

### Add New Endpoint
1. Add handler, request, response types to `.api`
2. Regenerate handler/types with goctl
3. Implement logic in `internal/logic/`
4. Run checklist: [new-handler-checklist.md](../../checklists/new-handler-checklist.md)

### Add Middleware
1. Implement middleware in `internal/middleware/`
2. Register in service context
3. Run checklist: [new-middleware-checklist.md](../../checklists/new-middleware-checklist.md)

## References
- [REST API Patterns](../../references/rest-api-patterns.md) — HTTP endpoints, CRUD, middleware, error handling
- [New API Definition Checklist](../../checklists/new-api-definition-checklist.md)
- [New Handler Checklist](../../checklists/new-handler-checklist.md)
- [New Middleware Checklist](../../checklists/new-middleware-checklist.md)
```

- [ ] **Step 2: Commit**

```bash
git add skills/rest-api/
git commit -m "feat: add rest-api skill"
```

---

### Task 8: Create skills/rpc-service/SKILL.md

**Files:**
- Create: `skills/rpc-service/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: rpc-service
description: >-
  Use when building gRPC services with go-zero — editing .proto files,
  implementing zrpc services, configuring service discovery with etcd,
  setting up load balancing, or running goctl rpc protoc commands.
  Also trigger when working with internal/server/, internal/logic/ for RPC
  services or dealing with service-to-service gRPC communication.
---

# go-zero RPC Service

## Core Principles

### Always Follow
- **Service discovery**: Register with etcd, discover via `zrpc.MustNewClient`
- **Proto-first**: Define service contract in `.proto` before implementation
- **Context propagation**: Pass `ctx` through all RPC calls for tracing
- **Structured errors**: Use `status.Error(codes.Code, msg)` for gRPC errors
- **Connection pooling**: Reuse RPC clients, don't create per-request

### Never Do
- Hard-code service endpoints (use service discovery)
- Put business logic in the server handler layer
- Mix gRPC error codes (use proper `codes.Code`)
- Block the RPC handler thread (use async for long operations)

## Common Workflows

### New RPC Service
1. Define service in `.proto` file
2. Generate: `goctl rpc protoc xxx.proto --go_out=. --go-grpc_out=. --zrpc_out=.`
3. Implement logic in `internal/logic/`
4. Details: [rpc-patterns.md](../../references/rpc-patterns.md)

### Add RPC Method
1. Add RPC method to `.proto`
2. Regenerate with goctl
3. Implement logic
4. Run checklist: [new-rpc-service-checklist.md](../../checklists/new-rpc-service-checklist.md)

## References
- [RPC Patterns](../../references/rpc-patterns.md) — gRPC services, service discovery, load balancing
- [New RPC Service Checklist](../../checklists/new-rpc-service-checklist.md)
```

- [ ] **Step 2: Commit**

```bash
git add skills/rpc-service/
git commit -m "feat: add rpc-service skill"
```

---

### Task 9: Create skills/database/SKILL.md

**Files:**
- Create: `skills/database/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: database
description: >-
  Use when working with databases in go-zero — sqlx operations, MongoDB queries,
  Redis caching, model generation with goctl model mysql datasource, schema design,
  or database migration planning. Also trigger when editing files under model/
  directories or configuring database connections in service configs.
---

# go-zero Database

## Core Principles

### Always Follow
- **Model isolation**: Keep data access in `model/`, expose interfaces
- **Context propagation**: Pass `ctx` to all database operations
- **Error handling**: Always check `LastInsertId()` and `RowsAffected()` return values
- **Connection management**: Use connection pooling, configure timeouts
- **Transactional consistency**: Use `sqlx.Tx` for multi-statement operations

### Never Do
- Discard database errors with `_` (especially `LastInsertId()`, `RowsAffected()`)
- Mix database logic with business logic
- Run queries without context timeout
- Use raw SQL without parameterization (SQL injection risk)

## Common Workflows

### New Database Table
1. Design schema and create table
2. Generate model: `goctl model mysql datasource -url="..." -table="xxx" -dir="./model"`
3. Inject model into ServiceContext
4. Details: [database-patterns.md](../../references/database-patterns.md)

### Add Redis Caching
1. Configure Redis in service config
2. Add cache layer in logic
3. Implement cache-aside pattern
4. Details: [database-patterns.md](../../references/database-patterns.md) (Redis section)

## References
- [Database Patterns](../../references/database-patterns.md) — sqlx, MongoDB, Redis caching
- [DB Migration Checklist](../../checklists/db-migration-checklist.md)
```

- [ ] **Step 2: Commit**

```bash
git add skills/database/
git commit -m "feat: add database skill"
```

---

### Task 10: Create skills/resilience/SKILL.md

**Files:**
- Create: `skills/resilience/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add skills/resilience/
git commit -m "feat: add resilience skill"
```

---

### Task 11: Create skills/observability/SKILL.md

**Files:**
- Create: `skills/observability/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: observability
description: >-
  Use when configuring or implementing observability in go-zero — distributed
  tracing (OpenTelemetry, Jaeger), Prometheus metrics, structured logging,
  health checks, SLO definition, or monitoring dashboards. Also trigger when
  adding middleware for logging/tracing or configuring `/healthz` endpoints.
---

# go-zero Observability

## Core Principles

### Always Follow
- **Structured logging**: Use `logx` with fields, not string formatting
- **Trace propagation**: Pass `ctx` through all layers and service calls
- **Health checks**: Implement `/healthz`, `/readyz` endpoints
- **Metrics**: Track latency (p50/p99), error rate, throughput per endpoint
- **Alerting**: Define SLOs and alert on burn rate, not raw thresholds

### Never Do
- Log sensitive data (passwords, tokens, PII) without redaction
- Use `fmt.Println` for operational logs (use `logx`)
- Skip trace context propagation across service boundaries
- Deploy without health check endpoints

## Common Workflows

### Add Tracing
1. Configure OpenTelemetry exporter in service config
2. Propagate `ctx` through all service calls
3. Details: [observability-patterns.md](../../references/observability-patterns.md)

### Setup Metrics
1. Define Prometheus metrics in service
2. Expose `/metrics` endpoint
3. Configure Grafana dashboard
4. Details: [observability-patterns.md](../../references/observability-patterns.md)

## References
- [Observability Patterns](../../references/observability-patterns.md) — Tracing, metrics, logging, health checks, SLO
```

- [ ] **Step 2: Commit**

```bash
git add skills/observability/
git commit -m "feat: add observability skill"
```

---

### Task 12: Create skills/deployment/SKILL.md

**Files:**
- Create: `skills/deployment/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: deployment
description: >-
  Use when planning or executing go-zero service deployment — Canary releases,
  Helm charts, HPA (Horizontal Pod Autoscaling), GitOps workflows, CI/CD
  pipelines, database migrations, or containerization. Also trigger when
  writing Dockerfiles, Kubernetes manifests, or deployment configs for go-zero.
---

# go-zero Deployment

## Core Principles

### Always Follow
- **Immutable infrastructure**: Build artifacts once, deploy with config changes
- **Graceful shutdown**: Handle SIGTERM, drain connections
- **Health probes**: Configure liveness and readiness probes in Kubernetes
- **Config externalization**: Use ConfigMap/Secret, never bake config into image
- **Migration safety**: Run DB migrations before code deploy, make backward-compatible

### Never Do
- Run database migrations automatically on service start (use init containers or separate job)
- Deploy without health check endpoints
- Skip canary/blue-green for critical services
- Override production config at deploy time manually

## Common Workflows

### Dockerize Service
1. Build multi-stage Dockerfile
2. Use goctl to generate Dockerfile: `goctl docker -go xxx.go`
3. Details: [deployment-patterns.md](../../references/deployment-patterns.md)

### Kubernetes Deploy
1. Create Helm chart or Kustomize overlay
2. Configure HPA based on CPU/memory or custom metrics
3. Set up Canary release with service mesh
4. Details: [deployment-patterns.md](../../references/deployment-patterns.md)

## References
- [Deployment Patterns](../../references/deployment-patterns.md) — Canary, Helm, HPA, GitOps, DB migrations
- [Production Readiness Checklist](../../checklists/production-readiness-checklist.md)
```

- [ ] **Step 2: Commit**

```bash
git add skills/deployment/
git commit -m "feat: add deployment skill"
```

---

### Task 13: Create skills/event-driven/SKILL.md

**Files:**
- Create: `skills/event-driven/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add skills/event-driven/
git commit -m "feat: add event-driven skill"
```

---

### Task 14: Create skills/testing/SKILL.md

**Files:**
- Create: `skills/testing/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: testing
description: >-
  Use when writing or planning tests for go-zero services — unit tests for
  logic layer, integration tests for API/RPC endpoints, contract tests for
  service boundaries, load tests, benchmarks, or chaos testing. Also trigger
  when setting up test infrastructure, mocking dependencies, or running
  go test with coverage requirements.
---

# go-zero Testing

## Core Principles

### Always Follow
- **Test the logic layer**: Business logic is the most valuable thing to test
- **Table-driven tests**: Use Go table-driven test pattern
- **Mock external dependencies**: Use interfaces for database, RPC clients
- **Test error paths**: Verify error handling, not just happy path
- **Coverage threshold**: Minimum 80% coverage for logic layer

### Never Do
- Mock the database for logic tests if integration tests exist
- Skip error scenario testing
- Write tests that depend on execution order
- Use `time.Sleep` for synchronization (use channels or `testing.T` helpers)

## Common Workflows

### Write Unit Test
1. Create test file alongside logic: `internal/logic/xxxlogic_test.go`
2. Mock dependencies via interfaces
3. Use table-driven tests
4. Details: [testing-patterns.md](../../references/testing-patterns.md)

### Write Integration Test
1. Start test server with test config
2. Send real HTTP/gRPC requests
3. Assert response and side effects
4. Details: [testing-patterns.md](../../references/testing-patterns.md)

## References
- [Testing Patterns](../../references/testing-patterns.md) — Unit, integration, contract, load, benchmark, chaos
```

- [ ] **Step 2: Commit**

```bash
git add skills/testing/
git commit -m "feat: add testing skill"
```

---

### Task 15: Create skills/security/SKILL.md

**Files:**
- Create: `skills/security/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: security
description: >-
  Use when implementing security in go-zero services — mTLS configuration,
  RBAC authorization, OAuth/OIDC integration, CORS settings, audit logging,
  API versioning strategy, error code standardization, pagination patterns,
  or OpenAPI/Swagger documentation. Also trigger when configuring service-to-service
  authentication or defining API governance rules.
---

# go-zero Security & API Governance

## Core Principles

### Always Follow
- **Defense in depth**: Auth at gateway + service-to-service mTLS
- **Least privilege**: RBAC with minimal required permissions
- **Audit trail**: Log all auth decisions and data mutations
- **API versioning**: Use URL-based versioning (`/v1/`, `/v2/`)
- **Consistent error codes**: Define error codes in `.api` files
- **CORS whitelist**: Explicitly list allowed origins

### Never Do
- Disable mTLS in production
- Hard-code credentials or secrets (use environment variables or secret manager)
- Return stack traces in API error responses
- Skip rate limiting on auth endpoints
- Expose internal error details to callers

## Common Workflows

### Setup Authentication
1. Configure JWT or OAuth middleware in service
2. Define RBAC roles and permissions
3. Apply middleware to protected routes
4. Details: [security-patterns.md](../../references/security-patterns.md)

### API Versioning
1. Define version strategy (URL-based: `/v1/`, `/v2/`)
2. Support multiple versions during migration
3. Deprecate old versions with sunset headers
4. Details: [api-governance-patterns.md](../../references/api-governance-patterns.md)

## References
- [Security Patterns](../../references/security-patterns.md) — mTLS, RBAC, OAuth, CORS, audit logging
- [API Governance Patterns](../../references/api-governance-patterns.md) — Versioning, error codes, pagination, OpenAPI
- [Production Readiness Checklist](../../checklists/production-readiness-checklist.md)
```

- [ ] **Step 2: Commit**

```bash
git add skills/security/
git commit -m "feat: add security skill"
```

---

### Task 16: Create skills/goctl/SKILL.md

**Files:**
- Create: `skills/goctl/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: goctl
description: >-
  Use when running goctl commands — goctl api go for API code generation,
  goctl rpc protoc for gRPC code generation, goctl model mysql datasource
  for database model generation, goctl docker for Dockerfile generation,
  goctl kube for Kubernetes manifests, or any other goctl subcommand.
  Also trigger when the user asks about code generation or scaffolding.
---

# goctl Commands

## Core Principles

### Always Follow
- **Generate, don't modify**: Never edit goctl-generated code directly
- **Regenerate safely**: Rerun goctl after `.api` / `.proto` changes
- **Customize in logic**: All custom code goes in `internal/logic/`
- **Verify generation**: Run `go build ./...` after generation

### Never Do
- Modify generated files in `internal/handler/`, `internal/types/`
- Commit generated code without verifying it compiles
- Mix manual and generated handler registrations

## Common Workflows

### API Code Generation
1. Define API spec in `.api` file
2. Run: `goctl api go -api xxx.api -dir .`
3. Implement logic in `internal/logic/`

### RPC Code Generation
1. Define service in `.proto` file
2. Run: `goctl rpc protoc xxx.proto --go_out=. --go-grpc_out=. --zrpc_out=.`
3. Implement logic in `internal/logic/`

### Model Generation
1. Create database table
2. Run: `goctl model mysql datasource -url="user:pass@tcp(host:port)/db" -table="xxx" -dir="./model"`
3. Inject model into ServiceContext

### Docker/K8s Generation
1. Run: `goctl docker -go xxx.go`
2. Run: `goctl kube deploy -name xxx -image xxx -o xxx.yaml`

## Full Command Reference
- [goctl Commands Reference](../../references/goctl-commands.md) — Complete goctl command reference with all flags
```

- [ ] **Step 2: Commit**

```bash
git add skills/goctl/
git commit -m "feat: add goctl skill"
```

---

### Task 17: Create agents/gozero-architect.md

**Files:**
- Create: `agents/gozero-architect.md`

- [ ] **Step 1: Write gozero-architect.md**

```markdown
---
name: gozero-architect
description: go-zero microservices architecture design agent — service decomposition, API vs RPC selection, data strategy, middleware planning
skills:
  - rest-api
  - rpc-service
  - database
  - resilience
  - event-driven
---

You are a go-zero microservices architect. Design systems following go-zero conventions.

## Core Design Rules

1. **Three-layer architecture**: Handler (routing/validation) → Logic (business) → Model (data). Never combine layers.

2. **Service communication choice**:
   - External clients → REST API (`.api` + goctl api go)
   - Internal service-to-service → gRPC (`.proto` + goctl rpc protoc)
   - Async/event-driven → Kafka or RabbitMQ + Outbox pattern

3. **Data strategy**:
   - One database per service (or shared with clear boundaries)
   - sqlx for relational, MongoDB for documents, Redis for caching/pubsub
   - Use CAS/optimistic locking for concurrent writes

4. **Resilience defaults**:
   - Circuit breaker on all external calls (`breaker.Do`)
   - Rate limiting on public endpoints (`limit.PeriodLimit`)
   - Context propagation through all layers

5. **Service discovery**: etcd for all services

## Output Format

For each design request, provide:

1. **Service Decomposition** — List each service with its responsibility
2. **Communication Map** — REST vs gRPC vs async for each interaction
3. **Data Design** — Tables/collections per service, caching strategy
4. **API/RPC Specs** — Example `.api` and `.proto` file content
5. **Middleware Plan** — Auth, logging, rate limiting per service
6. **Project Structure** — Directory layout following go-zero conventions
7. **Implementation Order** — Build sequence with dependencies

Reference the shared pattern guides at `../../references/` for detailed patterns.
```

- [ ] **Step 2: Commit**

```bash
git add agents/gozero-architect.md
git commit -m "feat: add gozero-architect agent"
```

---

### Task 18: Create agents/gozero-reviewer.md

**Files:**
- Create: `agents/gozero-reviewer.md`

- [ ] **Step 1: Write gozero-reviewer.md**

```markdown
---
name: gozero-reviewer
description: go-zero code review agent — validates three-layer architecture, context propagation, concurrency safety, and framework conventions
skills:
  - rest-api
  - rpc-service
  - database
  - resilience
  - security
---

You are a go-zero code reviewer. Review code against go-zero conventions and best practices.

## Review Checklist

### Architecture
- [ ] Three-layer separation: Handler → Logic → Model. No business logic in handlers.
- [ ] No direct database calls from handlers
- [ ] All generated code (`internal/handler/`, `internal/types/`) is untouched

### Context & Tracing
- [ ] `ctx context.Context` passed through all layers (handler → logic → model)
- [ ] Context passed to all RPC calls and database operations
- [ ] No `context.Background()` used in request paths (except main/setup)

### Concurrency Safety
- [ ] No read-modify-write without locking
- [ ] CAS/optimistic/pessimistic locking used for concurrent writes
- [ ] Distributed locks used for cross-service coordination

### Error Handling
- [ ] HTTP errors use `httpx.Error(w, err)`, not `fmt.Errorf` in handlers
- [ ] gRPC errors use `status.Error(codes.Code, msg)`
- [ ] No errors discarded with `_` (especially `LastInsertId()`, `RowsAffected()`)
- [ ] Error messages don't leak sensitive data

### Configuration
- [ ] No hardcoded values (use `conf.MustLoad` + ServiceContext)
- [ ] Secrets from environment variables or secret manager, not config files
- [ ] Reasonable defaults with override capability

### API Design
- [ ] Proper HTTP verbs (GET, POST, PUT, DELETE)
- [ ] Consistent error response format
- [ ] Pagination for list endpoints
- [ ] API versioning (`/v1/`, `/v2/`)

## Output Format

For each review request, provide findings with severity:
- **CRITICAL**: Must fix before merge (violates core go-zero rules)
- **HIGH**: Should fix (best practice violation)
- **MEDIUM**: Consider improving
- **LOW**: Nice to have

Reference the checklists at `../../checklists/` and best practices at `../../best-practices/overview.md`.
```

- [ ] **Step 2: Commit**

```bash
git add agents/gozero-reviewer.md
git commit -m "feat: add gozero-reviewer agent"
```

---

### Task 19: Create agents/gozero-best-practices.md

**Files:**
- Create: `agents/gozero-best-practices.md`

- [ ] **Step 1: Write gozero-best-practices.md**

```markdown
---
name: gozero-best-practices
description: go-zero production readiness validation agent — configuration, error handling, logging, security hardening, deployment checks
skills:
  - rest-api
  - rpc-service
  - database
  - observability
  - deployment
  - security
  - testing
---

You are a go-zero production readiness validator. Validate services against go-zero production standards.

## Validation Process

### 1. Configuration Review
- Verify `conf.MustLoad` used for all config loading
- Check ServiceContext is properly configured
- Confirm all environment variables are documented
- Validate secrets are not in config files

### 2. Error Handling Audit
- All errors are handled (no `_` discards)
- HTTP errors use `httpx.Error`, gRPC errors use `status.Error`
- Error messages are user-safe (no stack traces or internals)
- Logging captures error context for debugging

### 3. Observability Check
- Health check endpoints exist: `/healthz`, `/readyz`
- Structured logging with `logx` (not `fmt.Println`)
- Tracing context propagated through all layers
- Metrics exposed for latency, errors, throughput

### 4. Security Hardening
- mTLS enabled for service-to-service communication
- Rate limiting on all public endpoints
- CORS configured with explicit origins
- RBAC enforced on protected endpoints
- Audit logging for auth and data mutations

### 5. Resilience Verification
- Circuit breaker on all external calls
- Retry logic with backoff
- Graceful shutdown handling (SIGTERM)
- Timeout configured for all operations

### 6. Deployment Readiness
- Dockerfile uses multi-stage build
- Kubernetes probes configured (liveness, readiness)
- Database migrations are backward-compatible
- Config externalized via ConfigMap/Secret

## Output Format

For each validation request, run the `production-readiness-checklist.md` and report:

- **PASS**: Category meets all requirements
- **FAIL**: Specific items missing with file references
- **WARNING**: Items that need attention but aren't blockers

Reference:
- `../../checklists/production-readiness-checklist.md`
- `../../checklists/db-migration-checklist.md`
- `../../best-practices/overview.md`
- `../../troubleshooting/common-issues.md`
```

- [ ] **Step 2: Commit**

```bash
git add agents/gozero-best-practices.md
git commit -m "feat: add gozero-best-practices agent"
```

---

### Task 20: Update README.md and README_CN.md

**Files:**
- Create: `README.md`
- Create: `README_CN.md`

- [ ] **Step 1: Write README.md**

````markdown
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

### 10 Domain Skills

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
````

- [ ] **Step 2: Write README_CN.md**

````markdown
# zero-powers

go-zero 微服务开发插件 — 10 个领域技能 + 3 个专用智能体，面向 Claude Code 和 Codex 双平台。

## 安装

```bash
# Claude Code
claude plugins install zero-powers@claude-plugins-official

# 手动安装（项目级）
git clone https://github.com/zeromicro/zero-powers.git .claude/plugins/zero-powers

# 手动安装（用户级，所有项目可用）
git clone https://github.com/zeromicro/zero-powers.git ~/.claude/plugins/zero-powers
```

## 包含内容

### 10 个领域技能

| 技能 | 说明 |
|------|------|
| `rest-api` | REST API — `.api` 文件、handler/logic/model、HTTP 端点、中间件 |
| `rpc-service` | gRPC 服务 — `.proto` 文件、zrpc、服务发现、负载均衡 |
| `database` | 数据库 — sqlx、MongoDB、Redis 缓存、模型生成 |
| `resilience` | 韧性 — 熔断器、限流、并发锁、幂等性、Saga |
| `observability` | 可观测性 — 链路追踪、指标、日志、健康检查、SLO |
| `deployment` | 部署 — Canary、Helm、HPA、GitOps、数据库迁移 |
| `event-driven` | 事件驱动 — Kafka、RabbitMQ、CQRS、Outbox 模式 |
| `testing` | 测试 — 单元、集成、契约、负载、基准、混沌 |
| `security` | 安全 — mTLS、RBAC、OAuth、CORS、审计、API 治理 |
| `goctl` | 代码生成 — `goctl api go`、`goctl rpc protoc`、`goctl model` 等 |

### 3 个专用智能体

| 智能体 | 使用场景 |
|--------|----------|
| `@gozero-architect` | 编码前设计微服务架构 |
| `@gozero-reviewer` | 审查代码是否符合 go-zero 约定 |
| `@gozero-best-practices` | 验证生产就绪状态 |

### 共享资源

- 12 个模式参考指南
- 6 个开发检查清单
- 最佳实践和故障排除指南
- 多平台入门指南（Claude Code、Codex、Copilot、Cursor、Windsurf）
- 项目模板和演示示例

## 使用方式

处理 go-zero 文件（`.api`、`.proto`、包含 go-zero 的 `go.mod`）时技能自动加载。智能体手动调用：

```
@gozero-architect 设计一个用户管理微服务
@gozero-reviewer 审查当前改动
@gozero-best-practices 检查生产就绪状态
```

## 许可证

MIT — 与 go-zero 框架相同。
````

- [ ] **Step 3: Commit**

```bash
git add README.md README_CN.md
git commit -m "docs: add plugin README in English and Chinese"
```

---

### Task 21: Final verification

**Files:** None

- [ ] **Step 1: Verify complete directory structure**

```bash
cd /home/bt/projects/skill/zero-powers
find . -not -path './.git/*' -not -path './docs/*' -type f | sort
```

Expected file list:
```
.claude-plugin/marketplace.json
.claude-plugin/plugin.json
.codex/INSTALL.md
.gitignore
LICENSE
README.md
README_CN.md
agents/gozero-architect.md
agents/gozero-best-practices.md
agents/gozero-reviewer.md
best-practices/overview.md
checklists/README.md
checklists/db-migration-checklist.md
checklists/new-api-definition-checklist.md
checklists/new-handler-checklist.md
checklists/new-middleware-checklist.md
checklists/new-rpc-service-checklist.md
checklists/production-readiness-checklist.md
examples/README.md
examples/verify-tutorial.sh
examples/demo-project/...
getting-started/README.md
getting-started/claude-code-guide.md
getting-started/codex-guide.md
getting-started/copilot-guide.md
getting-started/cursor-guide.md
getting-started/windsurf-guide.md
references/api-governance-patterns.md
references/concurrency-patterns.md
references/database-patterns.md
references/deployment-patterns.md
references/event-driven-patterns.md
references/goctl-commands.md
references/observability-patterns.md
references/resilience-patterns.md
references/rest-api-patterns.md
references/rpc-patterns.md
references/security-patterns.md
references/testing-patterns.md
skills/database/SKILL.md
skills/deployment/SKILL.md
skills/event-driven/SKILL.md
skills/goctl/SKILL.md
skills/observability/SKILL.md
skills/resilience/SKILL.md
skills/rest-api/SKILL.md
skills/rpc-service/SKILL.md
skills/security/SKILL.md
skills/testing/SKILL.md
templates/project-CLAUDE.md
troubleshooting/common-issues.md
```

- [ ] **Step 2: Verify no excluded items present**

```bash
test ! -d skill-patterns && echo "PASS: no skill-patterns"
test ! -d .obsidian && echo "PASS: no .obsidian"
echo "Done"
```

- [ ] **Step 3: Verify all SKILL.md have valid YAML frontmatter**

```bash
for f in skills/*/SKILL.md; do
  echo "=== $f ==="
  head -5 "$f"
done
```

- [ ] **Step 4: Verify all agent files have valid YAML frontmatter**

```bash
for f in agents/*.md; do
  echo "=== $f ==="
  head -5 "$f"
done
```

- [ ] **Step 5: Final commit if any adjustments**

```bash
git status
git add -A
git diff --cached --stat
# git commit only if changes exist
```
