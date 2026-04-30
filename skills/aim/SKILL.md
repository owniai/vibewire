---
name: aim
description: Use ONLY when the user explicitly invokes /vibewire:aim. Do not auto-trigger based on codebase analysis, feature requests, or perceived implementation needs.
---

# Aim: From Task to Route

## Overview

读取项目上下文，澄清意图，判断任务类型与需求清晰度并路由。

<HARD-GATE>
aim 只做三件事：探索项目上下文、澄清意图、路由。禁止编写任何代码（测试、实现、配置）。
</HARD-GATE>

## Instruments

以下工具贯穿整个执行流程（aim 及路由后的流程），强烈推荐优先选用而非直接读取文件：

- **vibewire:scout agent** — 调研外部技术事实（兼容性、API 行为、最佳实践、许可证等）。适用于只需要结论无需代码实现验证的技术调研任务
- **vibewire:experimenter agent** — 验证技术假设，获取真实数据（API 结构、性能数据、行为验证等）。适用于需要编写代码实验证据的场景
- **Explore agent** — 快速只读搜索。适用于定位文件、grep 符号、查找定义，不需要自己深入阅读的场景
- **general-purpose agent** — 通用 agent，拥有全部工具。适用于需要多步操作的复杂研究任务
- **peek-code** — 基于 AST 的代码定义搜索，通过 Skill 工具加载。精确查找函数、类、接口等定义位置与签名，零误报。适用于理解文件结构、定位符号、快速扫描模块 API

**技术验证：** 任务依赖未确认的技术事实时，调用 scout 调研或 experimenter 实验，传入 task-id 和具体目标。不基于假设继续推理。

```
task-id：{task-tag}
{调研目标或实验目标}
```

## Process

### 1. Explore Project Context

读取项目文档与历史经验：
- `.vibewire/project.md` 和 `.vibewire/CHANGELOG.md` — 项目全貌。若不存在，提示用户先运行 `/vibewire:intro`
- `.vibewire/tech-research/knowledge.md`（若存在）— 全局技术调研知识库（简要结论、途径、经验）
- `.vibewire/evolve.md`（若存在）— 历史执行经验，用于规避已知的陷阱和偏差模式

聚焦探索与本次任务相关的模块：先通过 Skill 工具加载 `peek-code:peek-code` skill，再用 `peek` 基于签名快速定位相关定义，最后仅深入阅读评估本次变更所必需的文件，不发散探索无关模块

### 2. Clarify Intent

使用 AskUserQuestion 工具逐一提问以澄清用户意图：
- 每条消息只问一个问题
- 重点关注：用户要什么（what）、为什么（why）、有什么限制
- 充分理解后，呈现目标摘要供用户确认
- 用户确认后方可继续

### 3. Route

基于两个维度判断路由：**是否代码任务** + **需求是否清晰**。

**代码任务 + 需求清晰 → 执行流程：**
- **snap** — 小型代码变更，逻辑直截、不引入复杂新职责
- **build** — 常规代码变更，单次执行可连贯完成
- **plan** — 复杂代码变更，需拆分多阶段独立交付

**非代码任务 或 需求不清晰 → vibe：**
- **vibe** — 分析、调研、讨论、执行操作（git、脚本等），或澄清模糊需求

路由后读取对应流程文件（与本文件同目录）并按其指引执行，aim 职责结束：
vibe → `vibe.md` | snap → `snap.md` | build → `build.md` | plan → `plan.md`

使用 AskUserQuestion 展示全部四个路由选项，给出推荐及理由，附简短说明，由用户确认后路由。

## Key Principles

- **聚焦探索上下文** — 仅读取与本次任务相关的文件，充分但不发散
- **一次一个问题** — 不用多个问题淹没用户
- **零猜测** — 不确认之处向用户提问，禁用"我认为/应该是/大概"，改用事实陈述或列举可能性请求确认
- **准确路由** — 充分识别任务特征，确保路由准确，不应在执行过程中升级流程
- **善用 subagent** — 只需要结果、不需要自己深入细节的探索任务，委托 subagent 执行
- **忽略格式检查** — markdown lint 等文档格式告警一律忽略，内部规划文档不适用项目文档格式规范

## Anti-Pattern

- **"不读代码直接规划"** — 跳过上下文探索就评估任务，导致规划与现有实现脱节
- **"顺手写点代码"** — aim 阶段严禁编写任何代码，即使看起来很简单的修复也必须路由后再执行
- **"路由后直接实现"** — 路由后必须先读取流程文件，按流程文件的步骤和约束执行，不得跳过流程直接动手
- **"基于假设推理"** — 任务依赖未确认的技术事实时不调用 scout/experimenter 获取事实，而是基于猜测继续推理
- **"需求很明确，跳过澄清"** — 即使用户表述清晰，仍需确认目的、约束和成功标准。跳过澄清直接路由，常导致选错流程
