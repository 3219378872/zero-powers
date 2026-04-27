# zero-skills Checklists

> 完工前 / 上线前的强制性核对清单，供 `superpowers:verification-before-completion` 阶段消费。

## 使用方式

1. 完成代码编写后，**不要立即声明「完成」**
2. 根据任务类型打开对应清单
3. **逐项确认**——未勾选的项目必须解决或明确豁免（附理由）
4. 全部通过后才能进入代码评审 / 提交 / 合并流程

## 清单索引

| 清单 | 适用场景 | 关联 Superpowers 技能 |
|------|---------|---------------------|
| [new-handler-checklist.md](./new-handler-checklist.md) | 新增 HTTP Handler | TDD + verification-before-completion |
| [new-rpc-service-checklist.md](./new-rpc-service-checklist.md) | 新增 zrpc 服务或客户端 | brainstorming + TDD |
| [new-middleware-checklist.md](./new-middleware-checklist.md) | 新增中间件 | brainstorming + TDD |
| [new-api-definition-checklist.md](./new-api-definition-checklist.md) | 修改 `.api` 文件 / goctl 重新生成 | writing-plans + verification |
| [db-migration-checklist.md](./db-migration-checklist.md) | 数据库 schema 变更 | brainstorming + systematic-debugging |
| [production-readiness-checklist.md](./production-readiness-checklist.md) | 服务首次上生产 / 重大版本发布 | 全流程 |

## 清单编写规范

如果要新增或修改清单，遵循：

- 每项为**可二元判断**的问题（通过 / 不通过），不要模糊描述
- 每项提供**验证方式**（命令 / grep 模式 / 手动检查步骤）
- 分类：**阻塞型**（未通过不得上线） vs **建议型**（未通过需评估）
- 参考 `zero-skills/best-practices/` 对应章节时给出文件路径
