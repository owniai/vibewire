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

解析用户参数获取 `{N}-{name}`，完成以下检查：
1. 确认 `.vibewire/{N}-{name}/` 目录存在，且包含 `requirements.md` 和 `architecture.md`
   - 若缺失 → 提示用户先运行 `/vibewire:aim`
2. 创建 feature 分支：
   ```
   git checkout -b feature/{N}-{name}
   ```
3. 运行项目测试确认基线干净（如项目无测试则跳过）。若失败 → 暂停，报告失败信息，等待用户处理

### 2. Stage Design

调用 stager 产出全部阶段设计文档：

```
subagent_type: "stager"
description: "stager {N}-{name}"
prompt: |
  执行阶段设计。
  规划目录：.vibewire/{N}-{name}/
```

stager 完成后，从其状态报告中获取阶段列表。

### 3. Stage Loop

对 §2 获取的阶段列表中的每个 Stage，按顺序执行以下步骤。

#### 3.1 Implementer

```
subagent_type: "implementer"
description: "implementer Stage {M}-{name}"
prompt: |
  执行全部实现步骤。
  规划目录：.vibewire/{N}-{name}/
  Stage 文档：.vibewire/{N}-{name}/stage-{M}-{name}.md
```

根据 implementer 状态码处理：

| 状态 | 含义 | 处理方式 |
|------|------|----------|
| DONE | 完成 | 继续下一步骤 |
| DOC_ISSUE | 文档设计问题 | 按 §BLOCKED 处理流程修复 |
| BLOCKED | 连续修复失败 | 按 §BLOCKED 处理流程修复 |

#### 3.2 Reviewers

implementer 完成后，同时启动三个审查 agent：

```
subagent_type: "efficiency-reviewer"
description: "efficiency-reviewer Stage {M}-{name}"
prompt: |
  执行效率审查。
  规划目录：.vibewire/{N}-{name}/
  阶段：{M}-{name}
```

```
subagent_type: "quality-reviewer"
description: "quality-reviewer Stage {M}-{name}"
prompt: |
  执行质量审查。
  规划目录：.vibewire/{N}-{name}/
  阶段：{M}-{name}
```

```
subagent_type: "reuse-reviewer"
description: "reuse-reviewer Stage {M}-{name}"
prompt: |
  执行复用审查。
  规划目录：.vibewire/{N}-{name}/
  阶段：{M}-{name}
```

等待三个 agent 完成，根据各自输出的摘要判断结果：

- **全部无问题** → 继续下一 stage
- **存在至少一个问题** → 启动 resolver：

```
subagent_type: "resolver"
description: "resolver Stage {M}-{name}"
prompt: |
  执行审查修复。
  规划目录：.vibewire/{N}-{name}/
  阶段：{M}-{name}
```

resolver 完成后根据状态码处理：

| 状态 | 处理方式 |
|------|----------|
| DONE | 继续下一 stage |
| DONE_WITH_DEFERRED | 继续下一 stage（延后项已由 resolver 记录） |

### 4. Wrap-Up

所有 stage 完成后，并行调用 evolver 和 shadow-keeper：

```
subagent_type: "evolver"
description: "evolver {N}-{name}"
prompt: |
  执行经验提炼与漂移记录。
  规划目录：.vibewire/{N}-{name}/
```

```
subagent_type: "shadow-keeper"
description: "shadow-keeper {N}-{name}"
prompt: |
  执行影子 API 维护。
  规划目录：.vibewire/{N}-{name}/
```

两者完成后，运行全量测试验证：

- **Green** → 合并回主分支：

```bash
git checkout {main-branch}
git merge feature/{N}-{name}
```

- **Red** → 暂停，报告失败信息，等待用户处理

### 5. Completion

所有 stage 完成并合并后，报告整体完成状态。

## BLOCKED / DOC_ISSUE Handling

当 implementer 报告 DOC_ISSUE 或 BLOCKED 时：

1. 调用 stager 修复相关阶段文档：

```
subagent_type: "stager"
description: "stager fix stage-{M}-{name}"
prompt: |
  实现阶段报告了问题，请修改相关阶段文档以解决。
  规划目录：.vibewire/{N}-{name}/
  阶段：stage-{M}-{name}
  问题来源：implementer
  问题描述：{implementer 报告的问题内容}
```

2. 修复后重新调用 implementer（使用 §3.1 原模板）
3. 若 2 次修复后仍有问题 → 暂停，列出未解决问题，等待用户介入

暂停时输出：

```
Stage {M}-{name}: 执行失败（2 次自动修复后仍有问题）

Issues 列表：
- [列出所有未解决的问题]

请手动处理后，运行 /vibewire:go {N}-{name} 继续
```

## Key Principles

- **调度者定位** — 所有 agent 按调用独立启动新实例，不跨调用复用；每次调用严格使用流程步骤中的提示词模板，仅替换 `{变量}` 为实际值
- **严格按序执行** — 阶段按阶段列表中的依赖顺序执行，不跳阶段
- **状态码驱动** — 根据 agent 状态码决定后续动作，不猜测
- **自动修复** — agent BLOCKED 或 DOC_ISSUE 时自动调用 stager 修复文档后重试
- **暂停而非猜测** — 超过重试上限时暂停等用户
- **过程可追溯** — 所有过程记录在 `.vibewire/` 目录

## Error Handling

| 场景 | 处理方式 |
|------|----------|
| 规划目录不存在或缺少 requirements.md/architecture.md | 提示用户先运行 `/vibewire:aim` |
| 基线测试失败 | 暂停，报告失败信息，等待用户处理 |
| implementer DOC_ISSUE / BLOCKED | 调用 stager 修复文档，重新执行（最多 2 次） |
| 2 次修复后仍有问题 | 暂停，列出未解决问题，等待用户介入 |
| Wrap-Up 全量测试失败 | 暂停，报告失败信息，等待用户处理 |
