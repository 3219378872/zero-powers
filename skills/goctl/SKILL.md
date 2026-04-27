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
