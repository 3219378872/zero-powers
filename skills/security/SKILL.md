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
