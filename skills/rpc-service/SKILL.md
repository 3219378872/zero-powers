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
