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
