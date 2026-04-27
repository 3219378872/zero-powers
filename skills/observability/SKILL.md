---
name: observability
description: >-
  Use when configuring or implementing observability in go-zero — distributed
  tracing (OpenTelemetry, Jaeger), Prometheus metrics, structured logging,
  health checks, SLO definition, or monitoring dashboards. Also trigger when
  adding middleware for logging/tracing or configuring /healthz endpoints.
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
