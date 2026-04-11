---
name: go
description: "执行调度器 — 读取规划文档和全局设计，调度 stager/tester/implementer 完成从里程碑设计到代码实现的端到端流程。在 /global-design 完成后由用户调用 /go {seq-name} 启动。"
---

# Go：从设计到实现

## 概述

读取需求文档、架构设计和里程碑规划，通过调度三个专业 Agent（stager、tester、implementer），按里程碑和阶段迭代完成代码实现和测试验证。

## 流程

### 1. 初始化

- 确认 `.vibewire/{seq-name}/` 目录存在
- 读取 `.vibewire/{seq-name}/requirements.md` 了解项目信息
- 确认 `architecture.md` 和 `design.md` 存在，若缺失则提示用户先运行 `/spec` 和 `/global-design`
- 运行项目测试确认基线干净（如项目无测试则跳过）。若失败 → 暂停，报告失败信息，等待用户处理

<HARD-RULE>
所有 Agent 调用必须严格按照本文档中的模板使用 Agent 工具执行，不得自行修改、省略或替换模板内容。模板中的 `{变量}` 需替换为实际值。
</HARD-RULE>

### 2. 里程碑循环

对 `design.md` 中列出的每个里程碑，按顺序执行：

#### 2.1 里程碑设计

创建里程碑分支：

```
git checkout -b milestone-{N-name}
```

调用 stager 执行 Milestone Design：

```
subagent_type: "stager"
description: "stager Milestone Design {N-name}"
prompt: |
  执行 Milestone Design。
  里程碑：{N-name}
  规划目录：.vibewire/{seq-name}/
```

stager 完成后，进入阶段循环：

#### 2.2 阶段循环

对当前里程碑中的每个 stage，按顺序执行：

**步骤 1 — tester（Red）：**

```
subagent_type: "tester"
description: "tester Stage {N-M-name}"
prompt: |
  执行测试编写。
  规划目录：.vibewire/{seq-name}/
  Stage 文档：.vibewire/{seq-name}/milestone-{N-name}/stage-{N-M-name}.md
```

根据 tester 状态码处理：

| 状态 | 含义 | 处理方式 |
|------|------|----------|
| DONE | 测试编写完成 | 继续下一步骤 |
| DONE_WITH_CONCERNS | 完成但有顾虑 | 记录顾虑到日志，继续下一步骤 |
| BLOCKED | 无法继续 | 调用 stager 修复文档后重试（最多 2 次），仍失败则暂停等用户 |

**步骤 2 — implementer（Green）：**

tester 完成后，启动 implementer 执行全部步骤：

```
subagent_type: "implementer"
description: "implementer Stage {N-M-name}"
prompt: |
  执行全部实现步骤（Review → Implement → Verify → Self-Review → Commit）。
  规划目录：.vibewire/{seq-name}/
  Stage 文档：.vibewire/{seq-name}/milestone-{N-name}/stage-{N-M-name}.md
```

根据 implementer 状态码处理（同 tester 状态码表）。

**步骤 3 — review-code（审查）：**

implementer 完成且状态为 DONE 后，同时启动三个审查 agent：

```
subagent_type: "reuse-reviewer"
description: "reuse-reviewer Stage {N-M-name}"
prompt: |
  执行复用审查。
  规划目录：.vibewire/{seq-name}/
  里程碑：{N-name}
  阶段：{N-M-name}
```

```
subagent_type: "quality-reviewer"
description: "quality-reviewer Stage {N-M-name}"
prompt: |
  执行质量审查。
  规划目录：.vibewire/{seq-name}/
  里程碑：{N-name}
  阶段：{N-M-name}
```

```
subagent_type: "efficiency-reviewer"
description: "efficiency-reviewer Stage {N-M-name}"
prompt: |
  执行效率审查。
  规划目录：.vibewire/{seq-name}/
  里程碑：{N-name}
  阶段：{N-M-name}
```

等待三个 agent 完成，根据各自输出的一行摘要判断结果：

- **全部无问题** → 继续下一 stage
- **存在至少一个问题** → 启动修复 agent：

```
subagent_type: "resolver"
description: "resolver Stage {N-M-name}"
prompt: |
  执行审查修复。
  规划目录：.vibewire/{seq-name}/
  里程碑：{N-name}
  阶段：{N-M-name}
```

修复 agent 完成后检查其输出的测试结果：
  - **PASS** → 继续下一 stage
  - **FAIL** → 暂停，报告失败信息，等待用户处理

#### 2.3 里程碑总结

所有 stage 完成后，调用 summary-writer 生成总结：

```
subagent_type: "summary-writer"
description: "summary-writer milestone-{N}"
prompt: |
  生成里程碑总结。
  规划目录：.vibewire/{seq-name}/
  里程碑：{N-name}
```

提交总结，运行全量测试验证后合并回主分支：

```
git add .vibewire/{seq-name}/milestone-{N-name}/summary.md
git commit -m "[{seq-name}/m{N}] docs: 里程碑总结"
```

运行全量测试：

- **Green** → 合并回主分支：

```
git checkout {main-branch}
git merge milestone-{N-name}
```

- **Red** → 暂停，报告失败信息，等待用户处理

### 3. 最终总结

所有里程碑完成后，调用 summary-writer 生成最终总结：

```
subagent_type: "summary-writer"
description: "summary-writer final"
prompt: |
  生成最终总结。
  规划目录：.vibewire/{seq-name}/
```

### BLOCKED 处理流程

1. 调用 stager 修复相关文档：

```
subagent_type: "stager"
description: "stager fix {agent-name} issues"
prompt: |
  执行者报告了问题，请修改相关文档以解决。
  规划目录：.vibewire/{seq-name}/
  里程碑：{N-name}
  问题来源：{agent-name}
  问题描述：{agent 报告的问题内容}
```

2. 修复后重新调用报告问题的 agent（使用原模板）
3. 若 2 次修复后仍 BLOCKED → 暂停，列出未解决问题，等待用户介入

暂停时输出：

```
Stage {N-M-name}: {名称} 执行失败（2 次自动修复后仍有问题）

Issues 列表：
- [列出所有未解决的问题]

请手动处理后，运行 /go {seq-name} 继续
```

## 调用 Agent 通用规则

- 所有 agent 均按调用独立启动新实例，不跨调用复用
- implementer 一次运行全部步骤（Review → Implement → Verify → Self-Review → Commit），无需分次调用
- 每次调用时严格使用流程步骤中的提示词模板，仅替换 `{变量}` 为实际值
- 不得自行修改、省略或替换模板内容
- Agent 工作目录为项目根目录

## 关键原则

- **严格按序执行** — 里程碑和阶段按 design.md 中的依赖顺序执行，不跳阶段
- **TDD 串行** — tester 完成 Red 后再启动 implementer，严格遵循 Red → Green 循环
- **审批门控** — stager 每步输出后等待用户审批，不跳步
- **状态码驱动** — 根据 agent 状态码决定后续动作，不猜测
- **自动修复** — agent BLOCKED 时自动调用 stager 修复文档后重试
- **暂停而非猜测** — 超过重试上限时暂停等用户
- **过程可追溯** — 所有过程记录在 `.vibewire/` 目录

## 错误处理

| 场景 | 处理方式 |
| ------ | -------- |
| 规划目录不存在 | 提示用户先运行 `/spec` |
| architecture.md 或 design.md 不存在 | 提示文件缺失，先运行 `/spec` 和 `/global-design` |
| 基线测试失败 | 暂停，报告失败信息，等待用户处理 |
| 里程碑全量测试失败 | 暂停，报告失败信息，等待用户处理 |
| tester/implementer BLOCKED | 调用 stager 修复文档，修改后重新执行（最多 2 次） |
| 2 次修复后仍 BLOCKED | 暂停，列出未解决问题，等待用户介入 |
