# Dynamic Specs Review Design

## Overview

为 zero-powers 插件新增动态项目规范审查能力。核心思路：将项目特有规范从 CLAUDE.md 常驻上下文中解耦，存放在 `.claude/specs/` 目录，通过 skill 的动态上下文注入（`!`command``）按需加载，在 subagent 中执行结构化审查，避免主对话的长上下文疲劳。

## 设计原则

- **CLAUDE.md 放结论（what），specs 放完整规范（why + how）** — 分层互补，不重复
- **specs 只记录项目特有规范** — go-zero 通用最佳实践由 zero-powers 插件内置规则覆盖，不在 specs 中重复
- **用户完全自定义** — 插件提供模板骨架和初始化工具，具体内容由用户决定

## 新增/修改文件清单

```
zero-powers/
├── skills/
│   ├── structure-review/
│   │   └── SKILL.md              # 新增：动态注入 specs + 结构化审查
│   └── zero-init-specs/
│       └── SKILL.md              # 新增：交互式初始化 specs
├── templates/
│   ├── project-CLAUDE.md         # 已有，可能需微调
│   └── specs/                    # 新增：go-zero 默认 specs 模板骨架
│       ├── db-conventions.md
│       ├── test-strategy.md
│       ├── config-management.md
│       ├── error-codes.md
│       └── project-structure.md
├── agents/
│   └── gozero-reviewer.md       # 已有，升级：预加载 structure-review skill
```

用户项目侧生成的文件：

```
user-project/
└── .claude/
    └── specs/                    # 由 /zero-init-specs 生成
        ├── db-conventions.md
        ├── test-strategy.md
        └── ...（用户自行增删）
```

---

## 组件设计

### 1. `structure-review` Skill

动态注入用户项目规范并执行结构化审查。

**Frontmatter**:

```yaml
---
name: structure-review
description: >-
  Review project structure against specs in .claude/specs/.
  Use when reviewing architecture compliance, before merging branches,
  or when checking go-zero convention adherence.
context: fork
agent: general-purpose
allowed-tools: Bash(find *) Bash(cat *) Bash(wc *) Read Grep
---
```

**动态注入部分**：

skill 内容通过 `!`command`` 语法注入 `.claude/specs/` 下的所有 .md 文件：

~~~markdown
## 项目规范（动态注入）

```!
if [ -d ".claude/specs" ] && ls .claude/specs/*.md 1>/dev/null 2>&1; then
  for f in .claude/specs/*.md; do
    echo "=== $(basename "$f") ==="
    cat "$f"
    echo ""
  done
else
  echo "[WARNING] 未找到 .claude/specs/ 目录或其中没有 .md 文件。"
  echo "请先运行 /zero-powers:zero-init-specs 初始化项目规范。"
fi
```
~~~

**审查指令部分**：

```markdown
## 审查任务

根据上方注入的项目规范，对当前项目执行结构化审查。

### 审查流程
1. 扫描项目目录结构（find + tree）
2. 逐条对照 specs 中的每条规则
3. 对每条规则输出 pass/fail，违规项附具体文件路径和行号

### 输出格式

| 规则 | 来源 | 状态 | 详情 |
|------|------|------|------|
| Handler 禁止业务逻辑 | api-layer.md | ✅ PASS | — |
| Model 文件由 goctl 生成 | db-layer.md | ❌ FAIL | internal/model/usermodel.go:45 手动修改了生成代码 |

### 最终输出
- 通过率统计（pass / total）
- 所有 FAIL 项的修复建议
- 如果通过率 100%，输出确认信息
```

**关键决策**：
- `context: fork` — 在 subagent 中运行，specs 注入不占主对话上下文
- shell 脚本用 `for` 循环遍历 — 用户加减 specs 文件无需改动 skill
- 缺失 specs 时给出明确提示而非静默失败

---

### 2. `zero-init-specs` Skill

交互式初始化，扫描用户项目后识别项目特有规范并生成 specs 文件。

**Frontmatter**：

```yaml
---
name: zero-init-specs
description: >-
  Initialize .claude/specs/ by analyzing project codebase to discover
  project-specific conventions beyond go-zero defaults. Identifies
  unique patterns in database management, test coverage, config layout,
  CI/CD, and other areas, then generates structured spec files for review.
disable-model-invocation: true
allowed-tools: Bash(find *) Bash(cat *) Bash(ls *) Bash(grep *) Bash(wc *) Read Write Edit
---
```

**初始化流程**：

#### Step 1：扫描项目，识别项目特有规范

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

#### Step 2：划分领域，呈现 spec 清单

将识别出的规范按领域归类，向用户展示划分结果。示例输出：

```
检测到以下项目特有规范，建议划分为：

1. db-conventions.md — 使用 golang-migrate，迁移文件命名 {timestamp}_{desc}.sql ...
2. test-strategy.md — 覆盖率门禁 80%，集成测试用 testcontainers ...
3. config-management.md — 三环境分离（dev/staging/prod），密钥走 vault ...
4. error-codes.md — 错误码 6 位，按服务前缀分段（10xxxx=用户，20xxxx=订单）...

是否需要调整划分？可以合并、拆分或删除某些领域。
```

等待用户确认或调整。

#### Step 3：冲突检测与问询

如果识别出的规范之间存在冲突或模糊之处，逐条向用户确认。示例：

```
发现以下需要确认的点：

1. db-conventions 与 test-strategy 冲突：
   代码中集成测试直接连接本地 MySQL，但 CI 配置使用 testcontainers。
   → 生产规范应该是哪个？

2. config-management 模糊点：
   部分服务用 etc/config.yaml，部分用 etc/{env}.yaml。
   → 项目的统一约定是？
```

每次只问一个冲突点，等待用户回答后再继续下一个。

#### Step 4：生成 specs 文件

- 创建 `.claude/specs/` 目录
- 从插件模板（`${CLAUDE_SKILL_DIR}/../../templates/specs/`）读取结构参考
- 根据 Step 1-3 的分析和用户反馈，生成定制化的 spec 文件
- 每个文件头部添加注释说明该文件的用途和修改方式
- 每条规则包含：判定标准 + 违规示例 + 修复方式

#### Step 5：CLAUDE.md 精简建议

扫描项目 CLAUDE.md，识别与刚生成的 specs 内容重叠的段落，向用户展示：

```
以下 CLAUDE.md 段落已被 .claude/specs/ 覆盖：

1. [L45-L78] 数据库管理约定 → 已提取至 specs/db-conventions.md
2. [L102-L130] 测试要求 → 已提取至 specs/test-strategy.md
3. [L135-L158] 配置管理 → 已提取至 specs/config-management.md

可选操作：
A) 替换为引用 — 将上述段落替换为一行引用
   （如"详细规范见 .claude/specs/db-conventions.md，由 /structure-review 按需加载"）
B) 引入 superpowers 工作流 — 在 CLAUDE.md 中添加技能使用契约段落，
   将 structure-review 纳入审查链路
C) 两者都做
D) 跳过 — 保持 CLAUDE.md 不变
```

如果用户选择 B 或 C，生成的工作流契约段落参照 `templates/project-CLAUDE.md` 已有风格，将 `structure-review` 插入质量与调试阶段：

```markdown
### 质量与调试阶段

完工前：
1. superpowers:verification-before-completion
2. zero-powers:structure-review        ← 新增
3. 逐项对照 zero-powers/checklists/ 中对应清单
```

#### 输出摘要

- 生成的文件列表及简要说明
- 提示用户可随时编辑 `.claude/specs/` 中的文件
- 使用 `/zero-powers:structure-review` 执行审查
- 建议将 `.claude/specs/` 纳入版本控制

**关键决策**：
- `disable-model-invocation: true` — 只允许用户手动触发
- 用 `${CLAUDE_SKILL_DIR}` 引用插件内模板路径 — 不依赖工作目录
- 专注项目特有规范 — go-zero 通用最佳实践已由 zero-powers 内置覆盖

---

### 3. `gozero-reviewer` Agent 升级

在现有 agent 基础上预加载 `structure-review` skill。

**Frontmatter 变更**：

```yaml
---
name: gozero-reviewer
description: go-zero code review agent — validates three-layer architecture,
  context propagation, concurrency safety, framework conventions,
  and project-specific specs from .claude/specs/
skills:
  - zero-powers:structure-review
---
```

**Agent 指令新增段落**：

```markdown
## 项目特有规范审查

在完成 go-zero 通用规则审查后，如果 structure-review skill 注入了项目规范
（即 .claude/specs/ 存在且非空），则额外执行：

1. 按注入的 specs 逐条审查
2. 将结果合并到同一张审查表中
3. 区分来源：标注每条规则来自「go-zero 通用」还是「项目规范」

## 输出格式

最终输出一张合并表：

| 规则 | 来源 | 状态 | 详情 |
|------|------|------|------|
| Handler 禁止业务逻辑 | go-zero 通用 | ✅ PASS | — |
| 迁移文件命名 {timestamp}_{desc}.sql | 项目规范:db-conventions | ❌ FAIL | migrations/add_user.sql 缺少时间戳前缀 |
| 集成测试使用 testcontainers | 项目规范:test-strategy | ✅ PASS | — |

通过率统计分两部分：
- go-zero 通用规则：X/Y pass
- 项目规范：X/Y pass
- 总计：X/Y pass
```

**降级行为**：`.claude/specs/` 不存在时正常完成通用规则审查，跳过项目规范部分。

---

### 4. `templates/specs/` 模板集

作为 `zero-init-specs` 生成内容时的结构参考，不是直接复制给用户的。

**统一模板格式**：

```markdown
# {领域名称}

> 本文件记录项目特有的{领域}规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。

## 规则

### {规则名称}
- **判定标准**：{怎么判断是否符合}
- **违规示例**：{什么情况算违规}
- **修复方式**：{怎么改}
```

**预置模板清单**：

| 模板文件 | 覆盖领域 | 典型规则示例 |
|----------|----------|-------------|
| `db-conventions.md` | 数据库迁移、命名、分库策略 | 迁移文件命名格式、自定义 model 扩展位置 |
| `test-strategy.md` | 测试覆盖、mock 策略、CI 测试 | 覆盖率门禁、集成测试基础设施选型 |
| `config-management.md` | 多环境配置、密钥管理 | 环境配置分层方式、敏感值注入方式 |
| `error-codes.md` | 错误码体系、分段规范 | 错误码位数和分段策略、错误消息国际化 |
| `project-structure.md` | 目录布局、公共库、跨服务复用 | common/ 组织规则、服务间共享代码边界 |

**关键决策**：
- 模板是骨架不是内容 — `zero-init-specs` 根据项目实际分析填充具体规则
- 用户可生成不在模板之列的 spec 文件 — 完全允许
- 每条规则包含判定标准 — 这是 `structure-review` 做 pass/fail 判定的基础

---

## 数据流

### 流程 1：首次初始化

```
用户: /zero-powers:zero-init-specs

  Claude 扫描项目代码/配置/文档
    ↓
  识别项目特有规范，按领域归类
    ↓
  展示 spec 划分清单 → 用户确认/调整
    ↓
  检测规范间冲突 → 逐条问询用户
    ↓
  生成 .claude/specs/*.md
    ↓
  扫描 CLAUDE.md 重叠段落 → 用户选择精简方式
    （A 替换为引用 / B 引入 superpowers 工作流 / C 两者都做 / D 跳过）
    ↓
  输出摘要，建议纳入版本控制
```

### 流程 2：手动审查

```
用户: /zero-powers:structure-review

  skill 启动 subagent (context: fork)
    ↓
  !`for f in .claude/specs/*.md ...` 动态注入所有 specs
    ↓
  subagent 扫描项目文件结构和代码
    ↓
  逐条对照 specs 规则，判定 pass/fail
    ↓
  输出结构化审查表 + 通过率 + 修复建议
    ↓
  结果返回主对话（subagent 上下文释放）
```

### 流程 3：Agent 自动审查

```
gozero-reviewer agent 被调用
    ↓
  预加载 structure-review skill
    ↓
  先执行 go-zero 通用规则审查
    ↓
  structure-review 注入 .claude/specs/*
    ↓
  再执行项目特有规范审查
    ↓
  输出合并审查表（通用 + 项目，区分来源）
```

### 降级场景

| 场景 | 行为 |
|------|------|
| `.claude/specs/` 不存在 | structure-review 输出提示，建议运行 `/zero-init-specs` |
| `.claude/specs/` 为空 | 同上 |
| specs 中某条规则判定标准模糊 | 标记为 ⚠️ SKIP，附说明"判定标准不够明确，建议补充" |
| gozero-reviewer 调用但无 specs | 正常完成通用规则审查，跳过项目规范部分 |
