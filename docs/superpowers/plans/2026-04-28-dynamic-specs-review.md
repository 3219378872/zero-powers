# Dynamic Specs Review Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add dynamic project specs review capability to zero-powers plugin — users maintain `.claude/specs/` with project-specific rules, skills inject them on-demand via `!`command`` syntax, avoiding CLAUDE.md long-context fatigue.

**Architecture:** Three new/modified components: (1) `structure-review` skill with dynamic context injection runs in a forked subagent, (2) `zero-init-specs` skill scans project code to discover and generate project-specific specs interactively, (3) `gozero-reviewer` agent upgraded to preload `structure-review`. Template skeletons in `templates/specs/` provide structural reference.

**Tech Stack:** Claude Code skills (SKILL.md with YAML frontmatter), dynamic context injection (`!`command`` syntax), subagent execution (`context: fork`), shell scripting for injection logic.

**Design spec:** `docs/superpowers/specs/2026-04-28-dynamic-specs-review-design.md`

---

## File Structure

### New files

| File | Responsibility |
|------|---------------|
| `skills/structure-review/SKILL.md` | Dynamic injection of `.claude/specs/*.md` + structured review instructions + output format |
| `skills/zero-init-specs/SKILL.md` | Interactive project scanning, spec domain splitting, conflict detection, CLAUDE.md simplification |
| `templates/specs/db-conventions.md` | Template skeleton: database migration, naming, sharding conventions |
| `templates/specs/test-strategy.md` | Template skeleton: coverage gates, mock strategy, CI test stages |
| `templates/specs/config-management.md` | Template skeleton: multi-env config, secret management |
| `templates/specs/error-codes.md` | Template skeleton: error code numbering, segmentation |
| `templates/specs/project-structure.md` | Template skeleton: directory layout, shared libs, cross-service reuse |

### Modified files

| File | Change |
|------|--------|
| `agents/gozero-reviewer.md` | Add `skills` frontmatter for preloading, add project-specific review section |
| `skills/zero-powers/SKILL.md` | Add `structure-review` and `zero-init-specs` to domain skill list and quick reference |
| `templates/project-CLAUDE.md` | Insert `zero-powers:structure-review` into quality/debug workflow |
| `README.md` | Update skill count (10→12), add new skills to table |
| `README_CN.md` | Same updates in Chinese |

---

## Task 1: Create template spec skeletons

**Files:**
- Create: `templates/specs/db-conventions.md`
- Create: `templates/specs/test-strategy.md`
- Create: `templates/specs/config-management.md`
- Create: `templates/specs/error-codes.md`
- Create: `templates/specs/project-structure.md`

- [ ] **Step 1: Create `templates/specs/db-conventions.md`**

```markdown
# 数据库约定

> 本文件记录项目特有的数据库管理规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。

## 规则

### 迁移文件命名
- **判定标准**：迁移文件是否遵循 `{timestamp}_{description}.sql` 格式
- **违规示例**：`migrations/add_user.sql`（缺少时间戳前缀）
- **修复方式**：重命名为 `migrations/20260428120000_add_user.sql`

### 自定义 Model 扩展位置
- **判定标准**：自定义方法是否写在 `custom*Model` 上而非修改生成的 `default*Model`
- **违规示例**：在 `defaultUsersModel` 中添加 `FindByEmail` 方法
- **修复方式**：将自定义方法迁移到 `customUsersModel`

### 分库分表策略
- **判定标准**：分库分表规则是否记录在此并与代码实现一致
- **违规示例**：代码中使用 hash 分表但未在规范中声明
- **修复方式**：补充分表策略描述，或调整代码匹配规范
```

- [ ] **Step 2: Create `templates/specs/test-strategy.md`**

```markdown
# 测试策略

> 本文件记录项目特有的测试规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。

## 规则

### 覆盖率门禁
- **判定标准**：`go test -cover` 输出的覆盖率是否达到项目要求的阈值
- **违规示例**：新增 Logic 方法无任何测试，导致覆盖率低于门禁
- **修复方式**：为每个 Logic 方法至少编写一个成功路径 + 一个失败路径测试

### 集成测试基础设施
- **判定标准**：集成测试是否使用项目约定的基础设施（testcontainers / 本地数据库 / 其他）
- **违规示例**：项目约定 testcontainers，但新测试直连 localhost MySQL
- **修复方式**：改用 testcontainers 启动临时数据库实例

### 测试数据管理
- **判定标准**：测试数据是否通过约定方式管理（fixtures / factory / 内联构造）
- **违规示例**：测试直接操作生产数据库 schema
- **修复方式**：使用项目约定的测试数据构造方式
```

- [ ] **Step 3: Create `templates/specs/config-management.md`**

```markdown
# 配置管理

> 本文件记录项目特有的配置管理规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。

## 规则

### 多环境配置结构
- **判定标准**：配置文件是否按项目约定的环境分离方式组织
- **违规示例**：项目约定 `etc/{env}.yaml`，但新服务只有 `etc/config.yaml`
- **修复方式**：拆分为 `etc/dev.yaml`、`etc/staging.yaml`、`etc/prod.yaml`

### 敏感值管理
- **判定标准**：密钥、token 等是否通过约定方式注入（环境变量 / vault / sealed-secrets）
- **违规示例**：`etc/prod.yaml` 中明文写入数据库密码
- **修复方式**：改为 `${DB_PASSWORD}` 占位，通过环境变量或 secret manager 注入

### 配置分层策略
- **判定标准**：公共配置与服务专属配置是否按约定分层
- **违规示例**：每个服务重复定义相同的 Redis/etcd 连接信息
- **修复方式**：提取到公共配置层或使用配置继承机制
```

- [ ] **Step 4: Create `templates/specs/error-codes.md`**

```markdown
# 错误码体系

> 本文件记录项目特有的错误码规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。

## 规则

### 错误码格式
- **判定标准**：错误码是否符合项目约定的位数和分段策略
- **违规示例**：项目约定 6 位错误码（10xxxx=用户服务），新代码使用 3 位码 `401`
- **修复方式**：改用项目错误码体系，如 `100401` 表示用户服务认证失败

### 错误码集中定义
- **判定标准**：错误码是否在约定位置集中定义（如 `common/errorx/codes.go`）
- **违规示例**：在 Logic 层内联定义 `errors.New("user not found")`
- **修复方式**：在 `common/errorx/codes.go` 中定义 `ErrUserNotFound`，Logic 层引用

### 错误消息规范
- **判定标准**：面向客户端的错误消息是否符合项目约定（国际化 / 固定格式 / 脱敏）
- **违规示例**：错误消息暴露内部实现细节 `"sql: no rows in result set"`
- **修复方式**：返回用户友好消息 `"用户不存在"`，内部错误仅记录日志
```

- [ ] **Step 5: Create `templates/specs/project-structure.md`**

```markdown
# 项目结构

> 本文件记录项目特有的目录布局与模块组织规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。

## 规则

### 目录布局
- **判定标准**：服务目录是否遵循项目约定的组织方式
- **违规示例**：项目约定 `services/{name}/api/` 和 `services/{name}/rpc/`，新服务直接放在根目录
- **修复方式**：移动到 `services/` 下按约定结构组织

### 公共库边界
- **判定标准**：`common/` 或 `shared/` 中的代码是否符合跨服务复用规则
- **违规示例**：将只有一个服务使用的工具函数放入 `common/utils/`
- **修复方式**：移回使用该函数的服务内部，仅当多服务共用时才提取到 common

### 跨服务依赖方向
- **判定标准**：服务间依赖是否遵循项目约定的方向（如 gateway → services，不可反向）
- **违规示例**：某业务服务直接 import gateway 的内部包
- **修复方式**：通过 RPC 接口通信，不直接 import 其他服务的 internal 包
```

- [ ] **Step 6: Verify all template files exist**

Run: `ls -la templates/specs/`

Expected output shows 5 files:
```
db-conventions.md
test-strategy.md
config-management.md
error-codes.md
project-structure.md
```

- [ ] **Step 7: Commit**

```bash
git add templates/specs/
git commit -m "feat: add spec template skeletons for zero-init-specs"
```

---

## Task 2: Create `structure-review` skill

**Files:**
- Create: `skills/structure-review/SKILL.md`

- [ ] **Step 1: Create skill directory**

```bash
mkdir -p skills/structure-review
```

- [ ] **Step 2: Create `skills/structure-review/SKILL.md`**

````markdown
---
name: structure-review
description: >-
  Review project structure against specs in .claude/specs/.
  Use when reviewing architecture compliance, before merging branches,
  or when checking go-zero convention adherence. Also trigger when user
  asks to review project conventions, validate structure, or check specs.
context: fork
agent: general-purpose
allowed-tools: Bash(find *) Bash(cat *) Bash(wc *) Bash(tree *) Bash(grep *) Read Grep
---

# 项目规范审查

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

## 审查任务

根据上方注入的项目规范，对当前项目执行结构化审查。

如果上方显示 WARNING（未找到 specs），则输出提示信息并终止，不执行审查。

### 审查流程

1. 扫描项目目录结构（`find` + `tree`），建立项目全貌
2. 逐个读取 specs 文件，提取每条规则
3. 对每条规则：
   - 根据「判定标准」确定检查方式（文件扫描 / grep / 代码分析）
   - 执行检查，收集证据（具体文件路径和行号）
   - 判定 pass / fail / skip
4. 汇总输出结构化审查表

### 输出格式

| 规则 | 来源 | 状态 | 详情 |
|------|------|------|------|
| {规则名称} | {spec 文件名} | ✅ PASS | — |
| {规则名称} | {spec 文件名} | ❌ FAIL | {文件路径}:{行号} {违规描述} |
| {规则名称} | {spec 文件名} | ⚠️ SKIP | 判定标准不够明确，建议补充 |

### 最终输出

- 通过率统计：X/Y pass（Z skipped）
- 所有 FAIL 项的修复建议（引用 spec 中的「修复方式」）
- 如果通过率 100%，输出：✅ 所有项目规范检查通过
````

- [ ] **Step 3: Verify skill file exists and frontmatter is valid**

Run: `head -10 skills/structure-review/SKILL.md`

Expected: shows the YAML frontmatter with `name: structure-review`, `context: fork`, `agent: general-purpose`.

- [ ] **Step 4: Commit**

```bash
git add skills/structure-review/
git commit -m "feat: add structure-review skill with dynamic specs injection"
```

---

## Task 3: Create `zero-init-specs` skill

**Files:**
- Create: `skills/zero-init-specs/SKILL.md`

- [ ] **Step 1: Create skill directory**

```bash
mkdir -p skills/zero-init-specs
```

- [ ] **Step 2: Create `skills/zero-init-specs/SKILL.md`**

````markdown
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
````

- [ ] **Step 3: Verify skill file exists and frontmatter is valid**

Run: `head -12 skills/zero-init-specs/SKILL.md`

Expected: shows YAML frontmatter with `name: zero-init-specs`, `disable-model-invocation: true`.

- [ ] **Step 4: Commit**

```bash
git add skills/zero-init-specs/
git commit -m "feat: add zero-init-specs skill for interactive project specs initialization"
```

---

## Task 4: Upgrade `gozero-reviewer` agent

**Files:**
- Modify: `agents/gozero-reviewer.md`

- [ ] **Step 1: Update frontmatter to add skills preloading and updated description**

Replace the existing frontmatter (lines 1-4):

```markdown
---
name: gozero-reviewer
description: go-zero code review agent — validates three-layer architecture, context propagation, concurrency safety, and framework conventions
---
```

With:

```markdown
---
name: gozero-reviewer
description: go-zero code review agent — validates three-layer architecture, context propagation, concurrency safety, framework conventions, and project-specific specs from .claude/specs/
skills:
  - zero-powers:structure-review
---
```

- [ ] **Step 2: Add project-specific review section before the Output Format section**

Insert the following before line 44 (`## Output Format`):

```markdown
## Project-Specific Specs Review

After completing go-zero convention checks above, if the preloaded structure-review
skill injected project specs (i.e. `.claude/specs/` exists and is non-empty),
perform additional review:

1. Check each rule from the injected specs
2. Merge results into the same review table
3. Tag each finding with source: "go-zero 通用" or "项目规范:{spec-filename}"

If `.claude/specs/` does not exist or is empty, skip this section — only report
go-zero convention findings.

```

- [ ] **Step 3: Update Output Format section to include merged table**

Replace the existing Output Format section (lines 44-52):

```markdown
## Output Format

For each review request, provide findings with severity:
- **CRITICAL**: Must fix before merge (violates core go-zero rules)
- **HIGH**: Should fix (best practice violation)
- **MEDIUM**: Consider improving
- **LOW**: Nice to have

Reference the checklists at `../../checklists/` and best practices at `../../best-practices/overview.md`.
```

With:

```markdown
## Output Format

### Findings Table

| 规则 | 来源 | 严重度 | 状态 | 详情 |
|------|------|--------|------|------|
| {rule} | go-zero 通用 | CRITICAL/HIGH/MEDIUM/LOW | ✅/❌ | {details} |
| {rule} | 项目规范:{spec} | — | ✅/❌/⚠️ | {details} |

Severity levels (go-zero convention rules only):
- **CRITICAL**: Must fix before merge (violates core go-zero rules)
- **HIGH**: Should fix (best practice violation)
- **MEDIUM**: Consider improving
- **LOW**: Nice to have

Project spec rules use pass/fail/skip instead of severity levels.

### Summary

```
go-zero 通用规则：X/Y pass
项目规范：X/Y pass (Z skipped)
总计：X/Y pass
```

Reference the checklists at `../../checklists/` and best practices at `../../best-practices/overview.md`.
```

- [ ] **Step 4: Verify the full agent file reads correctly**

Run: `cat agents/gozero-reviewer.md`

Expected: frontmatter has `skills:` field, body contains both "Review Checklist" and "Project-Specific Specs Review" sections, Output Format shows merged table.

- [ ] **Step 5: Commit**

```bash
git add agents/gozero-reviewer.md
git commit -m "feat: upgrade gozero-reviewer agent with project specs preloading"
```

---

## Task 5: Update `zero-powers` entry skill

**Files:**
- Modify: `skills/zero-powers/SKILL.md`

- [ ] **Step 1: Add new skills to Domain Skills list**

After the existing `- **goctl** — API/RPC/model code generation commands` line (line 29), add:

```markdown
- **structure-review** — Dynamic project specs injection and structured review
- **zero-init-specs** — Interactive project specs initialization from codebase analysis
```

- [ ] **Step 2: Add new skills to Quick Reference table**

After the `| Generate code | goctl | ...` row (line 43), add:

```markdown
| Review project specs | structure-review | [.claude/specs/](../../.claude/specs/) |
| Initialize specs | zero-init-specs | [templates/specs/](../templates/specs/) |
```

- [ ] **Step 3: Verify the file reads correctly**

Run: `cat skills/zero-powers/SKILL.md`

Expected: Domain Skills list contains 12 entries including `structure-review` and `zero-init-specs`. Quick Reference table has 12 rows.

- [ ] **Step 4: Commit**

```bash
git add skills/zero-powers/SKILL.md
git commit -m "feat: add structure-review and zero-init-specs to entry skill"
```

---

## Task 6: Update `templates/project-CLAUDE.md`

**Files:**
- Modify: `templates/project-CLAUDE.md`

- [ ] **Step 1: Insert `structure-review` into quality/debug workflow**

Find the "完工前" section (lines 91-106). Replace:

```
1. superpowers:verification-before-completion
2. 逐项对照 zero-powers/checklists/ 中对应清单：
```

With:

```
1. superpowers:verification-before-completion
2. zero-powers:structure-review（如果 .claude/specs/ 存在）
3. 逐项对照 zero-powers/checklists/ 中对应清单：
```

- [ ] **Step 2: Add `structure-review` to the implementation phase table**

Find the implementation phase table (lines 69-79). After the last row (`| 消息队列 | ... |`), add:

```markdown
| 项目规范审查 | `.claude/specs/*.md`（动态注入） | `structure-review` |
```

- [ ] **Step 3: Verify changes look correct**

Run: `grep -n "structure-review" templates/project-CLAUDE.md`

Expected: appears in both the implementation table and the quality/debug workflow.

- [ ] **Step 4: Commit**

```bash
git add templates/project-CLAUDE.md
git commit -m "feat: add structure-review to project CLAUDE.md template"
```

---

## Task 7: Update README files

**Files:**
- Modify: `README.md`
- Modify: `README_CN.md`

- [ ] **Step 1: Update `README.md` — change skill count and add new skills**

Replace `### 10 Domain Skills` (line 20) with `### 12 Domain Skills`.

After the `goctl` row in the skill table (line 33), add:

```markdown
| `structure-review` | Project specs review — dynamic injection of `.claude/specs/`, structured pass/fail audit |
| `zero-init-specs` | Specs initialization — scans codebase for project-specific conventions, generates spec files |
```

- [ ] **Step 2: Update `README_CN.md` — same changes in Chinese**

Replace `### 10 个领域技能` (line 20) with `### 12 个领域技能`.

After the `goctl` row in the skill table (line 33), add:

```markdown
| `structure-review` | 项目规范审查 — 动态注入 `.claude/specs/`，结构化 pass/fail 审计 |
| `zero-init-specs` | 规范初始化 — 扫描代码库识别项目特有约定，生成 spec 文件 |
```

- [ ] **Step 3: Verify both files**

Run: `grep -c "12" README.md README_CN.md`

Expected: both files show at least 1 match for "12" (in the skill count heading).

- [ ] **Step 4: Commit**

```bash
git add README.md README_CN.md
git commit -m "docs: update READMEs with new structure-review and zero-init-specs skills"
```

---

## Task 8: End-to-end verification

- [ ] **Step 1: Verify all new files exist**

Run: `find skills/structure-review skills/zero-init-specs templates/specs -type f | sort`

Expected:
```
skills/structure-review/SKILL.md
skills/zero-init-specs/SKILL.md
templates/specs/config-management.md
templates/specs/db-conventions.md
templates/specs/error-codes.md
templates/specs/project-structure.md
templates/specs/test-strategy.md
```

- [ ] **Step 2: Verify all SKILL.md frontmatter is valid YAML**

Run: `for f in skills/structure-review/SKILL.md skills/zero-init-specs/SKILL.md; do echo "=== $f ==="; head -1 "$f"; done`

Expected: both files start with `---`.

- [ ] **Step 3: Verify dynamic injection shell command works**

Run: `mkdir -p /tmp/test-specs && echo "# Test" > /tmp/test-specs/test.md && cd /tmp && if [ -d "test-specs" ] && ls test-specs/*.md 1>/dev/null 2>&1; then for f in test-specs/*.md; do echo "=== $(basename "$f") ==="; cat "$f"; echo ""; done; fi && rm -rf /tmp/test-specs`

Expected: outputs `=== test.md ===` followed by `# Test`.

- [ ] **Step 4: Verify gozero-reviewer agent has skills field**

Run: `grep -A2 "skills:" agents/gozero-reviewer.md`

Expected: shows `skills:` followed by `- zero-powers:structure-review`.

- [ ] **Step 5: Verify skill count consistency**

Run: `ls skills/ | wc -l`

Expected: `13` (11 existing + 2 new: structure-review, zero-init-specs).

- [ ] **Step 6: Verify no uncommitted changes**

Run: `git status`

Expected: clean working tree.
