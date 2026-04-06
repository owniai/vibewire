---
name: go
description: "执行调度器 — 读取规划文档和全局设计，调度 stager/tester/implementer 完成从里程碑设计到代码实现的端到端流程。在 /global-design 完成后由用户调用 /go {seq}-{name} 启动。"
---

# Go：从设计到实现

## 概述

读取 `/spec` 输出的需求文档和架构设计、`/global-design` 输出的里程碑规划，通过调度三个专业 Agent（stager、tester、implementer），按里程碑和阶段迭代完成代码实现和测试验证。

## 流程

### 1. 初始化

- 确认 `.vibewire/{seq}-{name}/` 目录存在
- 读取 `requirements.md`、`architecture.md` 和 `design.md`
- 确认文件内容完整，否则提示用户先运行 `/spec` 和 `/global-design`
- 检测当前目录是否为 git 仓库，若不是则 `git init` 初始化
- 根据项目信息（语言、框架等）创建或更新 `.gitignore`
- 若仓库无任何提交，创建初始提交
- 运行项目测试确认基线干净（如项目无测试则跳过）。若失败 → 暂停，报告失败信息，等待用户处理

<HARD-RULE>
所有 Agent 调用必须严格按照本文档中的模板使用 Agent 工具执行，不得自行修改、省略或替换模板内容。模板中的 `{变量}` 需替换为实际值。
</HARD-RULE>

### 2. 里程碑循环

对 `design.md` 中列出的每个里程碑，按顺序执行：

#### 2.1 里程碑设计

创建里程碑分支：

```
git checkout -b milestone-{N}-{name}
```

调用 stager 执行 Milestone Design：

```
subagent_type: "stager"
description: "stager Milestone Design {N}-{name}"
prompt: |
  执行 Milestone Design。
  里程碑序号：{N}，里程碑名称：{name}
  规划目录：.vibewire/{seq-name}/
```

stager 完成后，提交设计文档并进入阶段循环：

```
git add .vibewire/{seq-name}/milestone-{N}-{name}/
git commit -m "docs(milestone-{N}): 里程碑设计文档"
```

#### 2.2 阶段循环

对当前里程碑中的每个 stage，按顺序执行：

**步骤 1 — tester（Red）：**

```
subagent_type: "tester"
description: "tester Stage {N}-{M}"
prompt: |
  执行测试编写。
  规划目录：.vibewire/{seq-name}/
  Stage 文档：.vibewire/{seq-name}/milestone-{N}-{name}/stage-{N}-{M}.md
```

根据 tester 状态码处理：

| 状态 | 含义 | 处理方式 |
|------|------|----------|
| DONE | 测试编写完成 | 继续下一步骤 |
| DONE_WITH_CONCERNS | 完成但有顾虑 | 记录顾虑到日志，继续下一步骤 |
| BLOCKED | 无法继续 | 调用 stager 修复文档后重试（最多 2 次），仍失败则暂停等用户 |
| NEEDS_CONTEXT | 需要额外信息 | 补充缺失上下文后调度新的 agent |

**步骤 2 — implementer（Green）：**

tester 完成后，启动 implementer 执行全部步骤：

```
subagent_type: "implementer"
description: "implementer Stage {N}-{M}"
prompt: |
  执行全部实现步骤（Review → Implement → Verify → Self-Review → Commit）。
  规划目录：.vibewire/{seq-name}/
  Stage 文档：.vibewire/{seq-name}/milestone-{N}-{name}/stage-{N}-{M}.md
```

根据 implementer 状态码处理（同 tester 状态码表）。

**步骤 3 — review-code（审查）：**

implementer 完成且状态为 DONE 后，执行 reuse, quality, and efficiency 三维度代码审查。从 `references/review-agents.md` 读取提示词模板，使用 Agent 工具同时启动三个 agent。

等待三个 agent 完成，根据各自输出的一行摘要判断结果：

- **全部无问题** → 继续下一 stage
- **存在至少一个问题** → 从 `references/review-approver.md` 读取提示词模板，使用 Agent 工具启动修复 agent。修复 agent 完成后检查其输出的测试结果：
  - **PASS** → 继续下一 stage
  - **FAIL** → 暂停，报告失败信息，等待用户处理

#### 2.3 里程碑总结

所有 stage 完成后，生成 `.vibewire/{seq-name}/milestone-{N}-{name}/summary.md`：

```markdown
# Milestone {N}: {里程碑名称} — 总结

## 完成概况
- 阶段数：{N}
- 总任务数：{N}

## 阶段完成情况
| Stage | 名称 | 任务数 | 状态 |
| ----- | ----- | ----- | ----- |
| {N}-{M} | {名称} | {N} | ✅ 通过 |

## 修改文件
- [列出所有新建和修改的文件]

## Issues 遗留
- [列出未解决的问题]
```

提交总结，运行全量测试验证后合并回主分支：

```
git add .vibewire/{seq-name}/milestone-{N}-{name}/summary.md
git commit -m "docs(milestone-{N}): 里程碑总结"
```

运行全量测试：

- **Green** → 合并回主分支：

```
git checkout {main-branch}
git merge milestone-{N}-{name}
```

- **Red** → 暂停，报告失败信息，等待用户处理

### 3. 最终总结

所有里程碑完成后，生成 `.vibewire/{seq-name}/final-summary.md`：

```markdown
# 最终总结 — {任务名称}

## 概况
- 规划目录：.vibewire/{seq-name}/
- 总里程碑数：{N}
- 总阶段数：{N}
- 总任务数：{N}

## 里程碑完成情况
| Milestone | 名称 | 阶段数 | 任务数 | 状态 |
| --------- | ----- | ----- | ----- | ----- |
| {N} | {名称} | {N} | {N} | ✅ 通过 |

## 修改文件汇总
- [列出所有新建和修改的文件]

## Issues 遗留
- [列出所有未解决的问题]

## 下一步
- 运行项目测试确认完整性
- 检查遗留问题是否需要处理
```

### BLOCKED 处理流程

1. 调用 stager 修复相关文档：

```
subagent_type: "stager"
description: "stager fix {agent-name} issues"
prompt: |
  执行者报告了问题，请修改相关文档以解决。
  规划目录：.vibewire/{seq-name}/
  里程碑序号：{N}，里程碑名称：{name}
  问题来源：{agent-name}
  问题描述：{agent 报告的问题内容}
```

2. 修复后重新调用报告问题的 agent（使用原模板）
3. 若 2 次修复后仍 BLOCKED → 暂停，列出未解决问题，等待用户介入

暂停时输出：

```
Stage {N}-{M}: {名称} 执行失败（2 次自动修复后仍有问题）

Issues 列表：
- [列出所有未解决的问题]

请手动处理后，运行 /go {seq}-{name} 继续
```

### NEEDS_CONTEXT 处理流程

1. 分析 agent 报告的上下文需求
2. 从项目文档或代码中提取所需信息
3. 在 agent 的 prompt 中补充所需上下文后重新调度

## 调用 Agent 通用规则

- stager、tester、implementer 均按调用独立启动新 agent 实例，不跨调用复用
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
| requirements.md、architecture.md 或 design.md 缺失 | 提示文档不完整，运行 `/spec` 和 `/global-design` |
| 基线测试失败 | 暂停，报告失败信息，等待用户处理 |
| 里程碑全量测试失败 | 暂停，报告失败信息，等待用户处理 |
| tester/implementer BLOCKED | 调用 stager 修复文档，修改后重新执行（最多 2 次） |
| 2 次修复后仍 BLOCKED | 暂停，列出未解决问题，等待用户介入 |
| tester/implementer NEEDS_CONTEXT | 提供上下文后重新调度 |
