---
name: go
description: Use ONLY when the user explicitly invokes /vibewire:go. Do not auto-trigger based on the existence of design documents or planning artifacts.
---

# Go: From Architecture to Implementation

## Overview

按阶段迭代调度专业 Agent，从阶段设计到代码实现的端到端交付。

<HARD-RULE>
你是调度者，不是执行者。专注于流程推进和 Agent 派发，任何时候不要深入实现细节——所有具体任务都必须派发给对应职责的 Agent 去操作。所有 Agent 调用必须严格按照本文档中的模板使用 Agent 工具执行，不得自行修改、省略或替换模板内容。模板中的 `{变量}` 需替换为实际值。
</HARD-RULE>

## Process

### 1. Initialize

从用户输入中解析 `PLAN-{N}-{name}`。完成以下检查：
1. 确认 `.vibewire/PLAN-{N}-{name}/` 目录存在，且包含 `requirements.md` 和 `architecture.md`；若缺失 → 提示用户先运行 `/vibewire:aim`
2. 记录当前分支名（后续合并需要），创建 feature 分支：
   ```
   {original-branch} = git rev-parse --abbrev-ref HEAD
   git checkout -b feature/PLAN-{N}-{name}
   ```

### 2. Stage Loop

确定阶段列表：从用户输入中解析阶段列表；当且仅当用户未提供阶段列表时，读取 `architecture.md` 的 Stage Plan 章节获取。按阶段列表顺序，对每个 Stage 执行以下步骤。

#### 2.1 Implementer

```
subagent_type: "vibewire:implementer"
description: "implementer Stage {M}-{name}"
prompt: |
  执行全部实现步骤。
  规划目录：.vibewire/PLAN-{N}-{name}/
  阶段：Stage {M}-{name}
```

根据 implementer 状态码处理：

| 状态 | 含义 | 处理方式 |
|------|------|----------|
| DONE | 完成 | 继续下一步骤 |
| BLOCKED | 修复失败 | 提示词添加 BLOCKED 原因，重新调用 implementer |

若重试两次后仍 BLOCKED → 暂停，等待用户介入：

```
Stage {M}-{name}: 执行失败（重试后仍有问题），详见 .vibewire/PLAN-{N}-{name}/log.md
```

#### 2.2 Reviewers

implementer 完成后，同时启动三个审查 agent：

```
subagent_type: "vibewire:efficiency-reviewer"
description: "efficiency-reviewer Stage {M}-{name}"
prompt: |
  执行效率审查。
  规划目录：.vibewire/PLAN-{N}-{name}/
  阶段：{M}-{name}
```

```
subagent_type: "vibewire:quality-reviewer"
description: "quality-reviewer Stage {M}-{name}"
prompt: |
  执行质量审查。
  规划目录：.vibewire/PLAN-{N}-{name}/
  阶段：{M}-{name}
```

```
subagent_type: "vibewire:reuse-reviewer"
description: "reuse-reviewer Stage {M}-{name}"
prompt: |
  执行复用审查。
  规划目录：.vibewire/PLAN-{N}-{name}/
  阶段：{M}-{name}
```

等待三个 agent 完成，根据各自输出的摘要判断结果：
- **存在至少一个 Critical 或 Major 问题** → 进入 §2.3
- **无 Critical 或 Major 问题** → 进入 §2.4

#### 2.3 Resolver

```
subagent_type: "vibewire:resolver"
description: "resolver Stage {M}-{name}"
prompt: |
  执行审查修复。
  规划目录：.vibewire/PLAN-{N}-{name}/
  阶段：{M}-{name}
```

resolver 完成后进入 §2.4。

#### 2.4 Update Shadow

从 implementer Status Report 中提取变更文件列表。若 resolver 曾执行，合并其 Status Report 中的变更文件列表。将合并后的列表传递给 shadow-writer：新增和修改文件原样传递，删除文件加 `DEL:` 前缀。

```
subagent_type: "vibewire:shadow-writer"
description: "shadow-writer Stage {M}-{name}"
prompt: |
  {变更文件列表，每行一个路径，删除文件前缀 DEL:}
```

完成后继续下一 stage。

### 3. Acceptance

所有 stage 完成后，进入验收修复循环。初始化 `round = 1`，最大修复轮次为 2。

#### 3.1 Accept

调用 acceptor 进行全量验收：

```
subagent_type: "vibewire:acceptor"
description: "acceptor PLAN-{N}-{name}"
prompt: |
  执行验收。
  规划目录：.vibewire/PLAN-{N}-{name}/
```

根据 acceptor 的 Verdict 处理：

| Verdict | 处理方式 |
|---------|----------|
| PASS | 继续进入 §4 Wrap-Up |
| CONDITIONAL | 进入 §3.2 Fix |
| FAIL | 暂停，列出 MISSING 需求，等待用户介入 |

暂停时输出：

```
PLAN-{N}-{name}: 验收未通过，详见 .vibewire/PLAN-{N}-{name}/acceptance.md
```

#### 3.2 Fix

启动 fixer 修复验收报告中的问题：

```
subagent_type: "vibewire:fixer"
description: "fixer PLAN-{N}-{name} round {round}"
prompt: |
  执行验收问题修复。
  规划目录：.vibewire/PLAN-{N}-{name}/
  修复轮次：{round}
```

fixer 完成后 `round++`，回到 §3.1 重新验收。若 `round > 2`（即已执行 2 轮修复后验收仍未 PASS）→ 暂停，列出遗留问题，等待用户介入：

```
PLAN-{N}-{name}: 验收修复循环结束，仍有遗留问题，详见 .vibewire/PLAN-{N}-{name}/acceptance.md
```

### 4. Wrap-Up

若验收阶段启用过 fixer（即 acceptor 曾返回 CONDITIONAL），合并所有 fixer Status Report 中的变更文件列表，调用 shadow-writer 更新 shadow 文件：

```
subagent_type: "vibewire:shadow-writer"
description: "shadow-writer PLAN-{N}-{name} acceptance fix"
prompt: |
  {变更文件列表，每行一个路径，删除文件前缀 DEL:}
```

调用 evolver：

```
subagent_type: "vibewire:evolver"
description: "evolver PLAN-{N}-{name}"
prompt: |
  执行经验提炼与健康度分析。
  规划目录：.vibewire/PLAN-{N}-{name}/
```

evolver 完成后，报告整体完成状态，然后询问用户如何合并，使用 AskUserQuestion 提供以下选项：
1. **Merge 到原始分支** — `git checkout {original-branch} && git merge feature/PLAN-{N}-{name}`
2. **Squash merge 到原始分支** — `git checkout {original-branch} && git merge --squash feature/PLAN-{N}-{name} && git commit`
3. **创建 Pull Request** — 使用 `gh pr create` 向 `{original-branch}` 发起 PR
4. **暂不合并** — 保留 feature 分支，稍后手动处理

用户选择后执行对应操作。

## Key Principles

- **调度者定位** — 所有 agent 按调用独立启动新实例，不跨调用复用；每次调用严格使用流程步骤中的提示词模板，仅替换 `{变量}` 为实际值
- **严格按序执行** — 阶段按阶段列表中的依赖顺序执行，不跳阶段
- **状态码驱动** — 根据 agent 状态码决定后续动作，不猜测
- **暂停而非猜测** — 超过重试上限时暂停等用户

## Anti-Pattern

- **"帮 agent 补充实现"** — 调度者的职责是派发和串联，不是替 agent 写代码或改文档。如果 agent 输出不完整，应该重试或修复 agent 的输入，而不是自己动手
- **"并行执行有依赖的 stage"** — stage 之间有明确的前后依赖（类型定义、共享状态等），并行执行会导致后续 stage 缺少前置产出而失败
- **"跳过审查直接进入下一 stage"** — 即使 implementer 报告 DONE，reviewer 和 resolver 仍是必要环节。跳过审查会累积技术债务
- **"修改 agent 的 prompt 模板"** — 模板是契约，不得自行增删字段或改写措辞。如果模板不满足需求，应该修改 SKILL.md 本身
- **"自动合并到主分支"** — 合并是影响共享状态的操作，必须由用户选择时机和方式

## Error Handling

| 场景 | 处理方式 |
|------|----------|
| 规划目录不存在或缺少 requirements.md/architecture.md | 提示用户先运行 `/vibewire:aim` |
| implementer BLOCKED | 重新执行（最多 2 次） |
| 重试后仍 BLOCKED | 暂停，列出未解决问题，等待用户介入 |
| acceptor FAIL | 暂停，列出 MISSING 需求，等待用户介入 |
| acceptor CONDITIONAL | 进入 §3.2 Fix 循环 |
| 验收修复 2 轮后仍未 PASS | 暂停，列出遗留问题，等待用户介入 |
