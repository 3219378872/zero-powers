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
