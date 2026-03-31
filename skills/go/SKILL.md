---
name: go
description: "执行调度器 — 读取规划文档，调度 stager/tester/implementer/reviewer 完成从阶段规划到代码实现的端到端流程。在 plan skill 完成后由用户调用 /go {seq}-{name} 启动。"
---

# Go：从规划到实现

## 概述

读取 plan skill 输出的需求文档和架构设计，通过调度四个专业 Agent（stager、tester、implementer、reviewer），按阶段和批次迭代完成代码实现、测试验证和代码审查。

## 流程

### 1. 初始化

- 确认 `.vibewire/{seq}-{name}/` 目录存在
- 读取 `requirements.md` 和 `architecture.md`
- 确认文件内容完整，否则提示用户先运行 `/plan`
- 检测当前目录是否为 git 仓库，若不是则 `git init` 初始化
- 根据项目信息（语言、框架等）创建或更新 `.gitignore`
- 若仓库无任何提交，创建初始提交

### 2. 阶段规划

<HARD-RULE>
所有 Agent 调用必须严格按照本文档中的模板使用 Agent 工具执行，不得自行修改、省略或替换模板内容。模板中的 `{变量}` 需替换为实际值。
</HARD-RULE>

调用 stager：

```
subagent_type: "stager"
description: "stager 总体阶段设计"
prompt: |
  执行首次调用。规划目录：.vibewire/{seq-name}/
```

### 3. 阶段循环

对 design.md 中列出的每个 Stage，按顺序执行：

#### 3.1 阶段设计

创建阶段分支：

```
git checkout -b stage-{N}-{name}
```

调用 stager：

```
subagent_type: "stager"
description: "stager 阶段设计 Stage {N}"
prompt: |
  执行逐阶段调用。
  阶段序号：{N}，阶段名称：{name}
  规划目录：.vibewire/{seq-name}/
```

提交阶段设计文档：

```
git add .vibewire/{seq-name}/stage-{N}-{name}/
git commit -m "docs(stage-{N}): 阶段设计文档"
```

#### 3.2 初始化

依次调用 tester 和 implementer 进行初始化：

**步骤 1 — tester init：**

```
subagent_type: "tester"
description: "tester init Stage {N}"
prompt: |
  init
  规划目录：.vibewire/{seq-name}/
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
```

**步骤 2 — implementer init：**

```
subagent_type: "implementer"
description: "implementer init Stage {N}"
prompt: |
  init
  规划目录：.vibewire/{seq-name}/
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
```

#### 3.3 批次循环

根据 handoff.md 中的任务分类，按 tasks.md 中的顺序分组执行：

**批次划分规则：**
- 连续的自测任务合并为一个批次
- 每个协作TDD任务单独为一个批次

**自测批次：**

```
subagent_type: "implementer"
description: "implementer 自测任务 Stage {N}"
prompt: |
  implement-self
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
  自测任务：Task {编号1}: {名称}, Task {编号2}: {名称}, ...
```

**协作TDD批次：**

步骤 1 — 同步调用 tester write-test 和 implementer implement-collab：

> **重要**：tester 和 implementer 必须在同一轮 Agent 工具调用中并行启动（多个 Agent 调用放在同一消息中）。

```
subagent_type: "tester"
description: "tester write-test Task {编号}"
prompt: |
  write-test
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
  目标任务：Task {编号} — {任务名称}
```

```
subagent_type: "implementer"
description: "implementer 实现 Task {编号}"
prompt: |
  implement-collab
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
  目标任务：Task {编号} — {任务名称}
```

步骤 2 — tester verify：

```
subagent_type: "tester"
description: "tester verify Task {编号}"
prompt: |
  verify
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
  验证范围：Task {编号} — {任务名称}
```

步骤 3 — 如果 verify 失败，implementer fix：

```
subagent_type: "implementer"
description: "implementer fix Task {编号}"
prompt: |
  fix
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
  失败详情：
  {从 tester-log.md 中提取失败详情}
```

修复后回到步骤 2（tester verify），重复直到通过。

步骤 4 — 验证通过后，implementer commit：

```
subagent_type: "implementer"
description: "implementer commit Stage {N}"
prompt: |
  commit
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
```

#### 3.4 审查循环

所有批次完成后，执行代码审查（最多 3 轮）：

**调用 reviewer × 4：**

按顺序调用 reviewer 4 次，每次使用以下模板：

| 次序 | 审查方向 | `{direction}` |
|------|----------|---------------|
| 1 | 设计符合性 | design-conformance |
| 2 | 代码复用 | code-reuse |
| 3 | 代码质量 | code-quality |
| 4 | 效率 | efficiency |

review 动作：

```
subagent_type: "reviewer"
description: "reviewer {direction} Stage {N}"
prompt: |
  review
  审查方向：{direction}
  审查规范：${CLAUDE_PLUGIN_ROOT}/references/{direction}.md
  规划目录：.vibewire/{seq-name}/
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
```

re-review 动作（修复后复审）：

```
subagent_type: "reviewer"
description: "reviewer re-review {direction} Stage {N}"
prompt: |
  re-review
  审查方向：{direction}
  审查规范：${CLAUDE_PLUGIN_ROOT}/references/{direction}.md
  规划目录：.vibewire/{seq-name}/
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
```

**收集审查结果：**

- 合并 4 次 review-results.md 中的所有 blocking 问题
- 如果无 blocking → 当前 Stage 审查通过

**有 blocking 问题时的修复流程（最多 3 轮）：**

步骤 1 — implementer fix（合并所有 blocking）：

```
subagent_type: "implementer"
description: "implementer fix 审查问题 Stage {N}"
prompt: |
  fix
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
  Blocking 问题：
  {列出所有 blocking 问题，含文件路径、问题描述、修复建议}
```

步骤 2 — tester verify：

```
subagent_type: "tester"
description: "tester verify 审查修复 Stage {N}"
prompt: |
  verify
  阶段目录：.vibewire/{seq-name}/stage-{N}-{name}/
  验证范围：审查修复后的全量验证
```

步骤 3 — 重新调用 reviewer × 4（re-review），使用上方 re-review 模板。

步骤 4 — 收集新的 blocking 问题：
- 如果仍有 blocking 且未超过 3 轮 → 回到步骤 1
- 如果 3 轮后仍有 blocking → 暂停，等待用户介入

**暂停时输出：**

```
Stage {N}: {名称} 审查未通过（{N} 轮修复后仍有 blocking 问题）

Blocking 问题列表：
- [列出所有未解决的 blocking 问题]

请手动处理后，运行 /go {seq}-{name} 继续
```

#### 3.5 阶段总结

审查通过后，生成 `.vibewire/{seq}-{name}/stage-{N}-{name}/summary.md`：

```markdown
# Stage {N}: {阶段名称} — 总结

## 完成概况
- 总任务数：{N}
- 协作TDD任务：{N} 个
- 自测任务：{N} 个

## 审查结果
- 审查轮次：{N}
- 发现问题：blocking {N} 个 / major {N} 个 / minor {N} 个
- 已修复：blocking {N} 个

## 修改文件
- [列出所有新建和修改的文件]

## 审查遗留
- [列出未修复的 major/minor 问题]
```

提交总结文档并合并回主分支：

```
git add .vibewire/{seq-name}/stage-{N}-{name}/summary.md
git commit -m "docs(stage-{N}): 阶段总结"
git checkout {main-branch}
git merge stage-{N}-{name}
```

### 4. 最终总结

所有 Stage 完成后，生成 `.vibewire/{seq}-{name}/final-summary.md`：

```markdown
# 最终总结 — {任务名称}

## 概况
- 规划目录：.vibewire/{seq}-{name}/
- 总阶段数：{N}
- 总任务数：{N}

## 阶段完成情况
| Stage | 名称 | 任务数 | 审查轮次 | 状态 |
|-------|------|--------|----------|------|
| 1 | {名称} | {N} | {N} | ✅ 通过 |

## 修改文件汇总
- [列出所有新建和修改的文件]

## 审查遗留问题
- [列出所有阶段中未修复的 major/minor 问题]

## 下一步
- 运行项目测试确认完整性
- 检查审查遗留问题是否需要处理
```

## 调用 Agent 通用规则

- Agent 通过 Agent 工具调用，subagent_type 使用对应 agent 名称
- 每次调用时严格使用流程步骤中的提示词模板，仅替换 `{变量}` 为实际值
- 不得自行修改、省略或替换模板内容
- Agent 工作目录为项目根目录

## 关键原则

- **严格按序执行** — Stage 之间按 design.md 中的依赖顺序执行，不跳阶段
- **批次不交叉** — 一个批次完成后再进入下一个批次，不并发
- **审查统一修复** — 四方向审查完成后统一修复，修复后重新跑全部方向
- **暂停而非猜测** — 超过重试上限时暂停等用户，不自行猜测或跳过
- **过程可追溯** — 所有过程记录在 `.vibewire/` 目录，便于回溯

## 错误处理

| 场景 | 处理方式 |
|------|----------|
| 规划目录不存在 | 提示用户先运行 `/plan` |
| requirements.md 或 architecture.md 缺失 | 提示文档不完整 |
| 批次内 verify 反复失败 | tester 记录问题到 tester-issues.md，等待处理 |
| 审查 3 轮后仍有 blocking | 暂停，列出未解决问题，等待用户介入 |
| Agent 输出 issues 文件 | 调用 stager 修改相关文档（design.md / tasks.md），修改后重新执行当前批次 |
