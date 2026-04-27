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
