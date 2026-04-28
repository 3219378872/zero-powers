---
name: zero-init-specs
description: >-
  Initialize .claude/specs/ by analyzing project codebase to discover
  project-specific conventions beyond go-zero defaults. Identifies unique
  patterns in database management, test coverage, config layout, CI/CD,
  and other areas, then generates structured spec files for review.
disable-model-invocation: true
allowed-tools: Bash(find *) Bash(cat *) Bash(ls *) Bash(grep *) Bash(wc *) Bash(tree *) Bash(mkdir *) Read Write Edit
---

# 初始化项目规范

为当前 go-zero 项目生成 `.claude/specs/` 目录，存放项目特有的架构规范。
这些规范专注于 go-zero 通用最佳实践之外的项目约定，由 `/zero-powers:structure-review` 按需加载审查。

## 模板参考

以下路径包含 spec 文件的结构骨架，用于参考格式（不直接复制给用户）：
- `${CLAUDE_SKILL_DIR}/../../templates/specs/`

每条规则的标准格式：

```markdown
### {规则名称}
- **判定标准**：{怎么判断是否符合}
- **违规示例**：{什么情况算违规}
- **修复方式**：{怎么改}
```

## 初始化流程

### Step 1：扫描项目，识别项目特有规范

深度扫描项目代码和配置，识别 go-zero 通用最佳实践之外的项目特有约定。

重点关注领域：
- **数据库管理** — 迁移工具选型、命名约定、分库分表策略、自定义 model 扩展模式
- **测试策略** — 覆盖率要求、mock 策略、测试数据管理、CI 中的测试阶段划分
- **配置文件管理** — 多环境配置结构、敏感值管理方式、配置分层策略
- **CI/CD 流程** — 构建流水线、部署策略、代码质量门禁
- **公共库约定** — common/ 或 shared/ 的组织方式、跨服务复用规则
- **错误码体系** — 项目自定义的错误码规范、分段策略
- **日志/监控** — 项目特有的日志格式、自定义 metric 命名
- **其他** — 任何从代码中识别出的、不属于 go-zero 默认约定的模式

识别方法：
- 读取现有 CLAUDE.md / README / docs 中的约定描述
- 分析目录结构和文件命名中的隐含模式
- 检查 Makefile / CI 配置 / docker-compose 中的流程约定
- 扫描代码中的重复模式（如错误处理风格、日志调用方式）

### Step 2：划分领域，呈现 spec 清单

将识别出的规范按领域归类，向用户展示划分结果：

```
检测到以下项目特有规范，建议划分为：

1. {filename}.md — {概述} ...
2. {filename}.md — {概述} ...

是否需要调整划分？可以合并、拆分或删除某些领域。
```

等待用户确认或调整后再继续。

### Step 3：冲突检测与问询

如果识别出的规范之间存在冲突或模糊之处，逐条向用户确认：

```
发现以下需要确认的点：

1. {spec A} 与 {spec B} 冲突：
   {冲突描述}
   → 项目规范应该是哪个？
```

每次只问一个冲突点，等待用户回答后再继续下一个。
如果没有冲突，跳过此步骤。

### Step 4：生成 specs 文件

- 创建 `.claude/specs/` 目录
- 参考 `${CLAUDE_SKILL_DIR}/../../templates/specs/` 中的结构骨架
- 根据 Step 1-3 的分析和用户反馈，生成定制化的 spec 文件
- 每个文件头部添加说明注释：

```markdown
# {领域名称}

> 本文件记录项目特有的{领域}规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。
```

- 每条规则包含：判定标准 + 违规示例 + 修复方式

### Step 5：CLAUDE.md 精简建议

扫描项目 CLAUDE.md（如果存在），识别与刚生成的 specs 内容重叠的段落，向用户展示：

```
以下 CLAUDE.md 段落已被 .claude/specs/ 覆盖：

1. [L{start}-L{end}] {段落描述} → 已提取至 specs/{filename}.md
2. ...

可选操作：
A) 替换为引用 — 将上述段落替换为一行引用
   （如"详细规范见 .claude/specs/{filename}.md，由 /structure-review 按需加载"）
B) 引入 superpowers 工作流 — 在 CLAUDE.md 中添加技能使用契约段落，
   将 structure-review 纳入审查链路（质量与调试阶段）：
   1. superpowers:verification-before-completion
   2. zero-powers:structure-review
   3. 逐项对照 zero-powers/checklists/ 中对应清单
C) 两者都做
D) 跳过 — 保持 CLAUDE.md 不变
```

等待用户选择后执行对应操作。

### 输出摘要

完成后输出：
- 生成的文件列表及每个文件包含的规则数
- 提示：可随时编辑 `.claude/specs/` 中的文件
- 提示：使用 `/zero-powers:structure-review` 执行审查
- 建议：将 `.claude/specs/` 纳入版本控制
