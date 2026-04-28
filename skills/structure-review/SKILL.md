---
name: structure-review
description: >-
  Review project structure against specs in .claude/specs/.
  Use when reviewing architecture compliance, before merging branches,
  or when checking go-zero convention adherence. Also trigger when user
  asks to review project conventions, validate structure, or check specs.
context: fork
agent: general-purpose
allowed-tools: Bash(find *) Bash(cat *) Bash(wc *) Bash(tree *) Bash(grep *) Bash(ls *) Read Grep
---

# 项目规范审查

## 加载项目规范

第一步，执行以下命令加载 `.claude/specs/` 下的所有规范文件：

```bash
if ls .claude/specs/*.md 1>/dev/null 2>&1; then
  for f in .claude/specs/*.md; do
    echo "=== $(basename "$f") ==="
    cat "$f"
    echo ""
  done
else
  echo "[WARNING] 未找到 .claude/specs/"
fi
```

如果命令输出 WARNING，则输出「请先运行 /zero-powers:zero-init-specs 初始化项目规范」并终止，不执行审查。

## 审查任务

根据上方命令加载的项目规范，对当前项目执行结构化审查。

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
