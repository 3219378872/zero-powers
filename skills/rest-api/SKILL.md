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
