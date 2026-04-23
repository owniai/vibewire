---
name: aim
description: Use ONLY when the user explicitly invokes /vibewire:aim. Do not auto-trigger based on codebase analysis, feature requests, or perceived implementation needs.
---

# Aim: From Task to Plan

## Overview

读取项目上下文，评估任务规模，选择并执行对应的规划与实现流程。

## Process

### 1. Explore Project Context

读取项目文档与历史经验：
- `.vibewire/project.md` 和 `.vibewire/CHANGELOG.md` — 项目全貌。若不存在，提示用户先运行 `/vibewire:intro`
- `.vibewire/tech-research.md`（若存在）— 全局技术调研摘要（简要结论、途径、经验）
- `.vibewire/evolve.md`（若存在）— 历史执行经验，用于规避已知的陷阱和偏差模式

聚焦探索与本次任务相关的模块：若 `.shadow/` 存在，先读取相关 shadow files 建立模块全貌，再按需定位重点源文件深入阅读；仅读取评估本次变更所必需的文件，不发散探索无关模块

> **Shadow Files**：`.shadow/` 是源文件的声明镜像（类似 .h 头文件），目录结构与源文件严格同构（如 `src/utils/helper.ts` 对应 `.shadow/src/utils/helper.ts`），保留 import、类型、接口、类签名、函数签名，省略函数体；行尾 `// L{start}-{end}` 标注指向源文件位置。

<HARD-GATE>
本阶段禁止编写任何代码（测试代码、实现代码、配置代码均不允许）。aim 只负责探索项目上下文、理解需求、评估任务规模并路由到对应流程。所有实现工作由路由后的流程文件指导执行。
</HARD-GATE>

### 2. Assess Task Scope & Route

判断任务应走 minimal、express 还是 comprehensive 流程。

**走 minimal — 小型任务：**
- 实现步骤明确，代码逻辑直截了当，无需复杂 review
- 即使需要前期调研或思考，只要最终实现步骤聚焦即可
- 典型场景：bug 修复、新增/重写函数、配置调整、性能优化、补充测试、安全修复、机械性重构、引入新依赖但用法明确
- 不涉及新增结构性单元（模块/子系统）或跨模块协调

**走 express — 常规任务：**
- 涉及跨模块协调：变更分布在多个模块中，存在依赖关系需要协调
- 新增结构性单元：新增模块/子系统，或进行结构性变更（拆分/重组）
- 较大规模的集成工作：如第三方服务集成，即使有既有模式可复用

**走 comprehensive — 复杂任务：**
- 需求模糊，范围不确定，需要澄清和发现
- 存在可独立交付的中间节点，需要分阶段规划
- 涉及架构层面的设计决策

**路由：** 基于信号匹配，读取对应的流程文件（与本文件同目录）并按其指引执行。路由完成后，aim 阶段的职责结束——后续所有操作（包括需求澄清、TDD、实现）均由流程文件驱动，不在此阶段执行任何代码变更。
- minimal 信号 → `minimal.md`
- express 信号 → `express.md`
- comprehensive 信号 → `comprehensive.md`

使用 AskUserQuestion 向用户说明评估结果，给出推荐的路由方向及理由，由用户确认。

## Key Principles

- **聚焦探索上下文** — 先 shadow 后源文件，仅读取与本次任务相关的文件，充分但不发散
- **聚焦已确认范围** — 评估和路由限定在实际需求范围内
- **忽略格式检查** — markdown lint 等文档格式告警一律忽略，内部规划文档不适用项目文档格式规范

## Anti-Pattern

- **"不读代码直接规划"** — 跳过上下文探索就评估任务，导致规划与现有实现脱节
- **"顺手写点代码"** — aim 阶段严禁编写任何代码，即使看起来很简单的修复也必须路由后再执行
- **"路由后直接实现"** — 路由后必须先读取流程文件，按流程文件的步骤和约束执行，不得跳过流程直接动手
