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
