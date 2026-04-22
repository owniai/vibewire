---
name: aim
description: Use ONLY when the user explicitly invokes /vibewire:aim. Do not auto-trigger based on codebase analysis, feature requests, or perceived implementation needs.
---

# Aim: From Task to Plan

## Overview

读取项目上下文，评估任务规模，选择并执行对应的规划与实现流程。

## Checklist

你必须为以下每个项目创建任务并按顺序完成：
1. **Explore Project Context** — 读取项目文档，若无则提示运行 /vibewire:intro
2. **Assess Task Scope & Route** — 评估任务规模，选择并加载对应流程

## Process

### 1. Explore Project Context

读取项目文档与历史经验：
- `.vibewire/project.md` 和 `.vibewire/CHANGELOG.md` — 项目全貌。若不存在，提示用户先运行 `/vibewire:intro`
- `.vibewire/tech-research.md`（若存在）— 技术调研结论，为需求澄清和架构设计提供技术事实基线
- `.vibewire/evolve.md`（若存在）— 历史执行经验，用于规避已知的陷阱和偏差模式

聚焦探索与本次任务相关的模块：若 `.shadow/` 存在，先读取相关 shadow files 建立模块全貌，再按需定位重点源文件深入阅读；仅读取评估本次变更所必需的文件，不发散探索无关模块

> **Shadow Files**：`.shadow/` 是源文件的声明镜像（类似 .h 头文件），目录结构与源文件严格同构（如 `src/utils/helper.ts` 对应 `.shadow/src/utils/helper.ts`），保留 import、类型、接口、类签名、函数签名，省略函数体；行尾 `// L{start}-{end}` 标注指向源文件位置。

### 2. Assess Task Scope & Route

评估任务规模，判断是否需要收窄范围，然后路由到对应的流程。

**适用 express.md 的信号：**
- 用一句话能描述完整交付物
- 变更沿单一链路推进，无分支
- 无独立的中间交付价值节点
- 即使存在可拆分的中间节点，拆分后各部分工作量均较小，合并执行的开销低于拆分的开销

**适用 comprehensive.md 的信号：**
- 描述了多个独立子系统或能力维度
- 存在可独立交付且工作量充足的中间节点
- 有明确的"先做 X 才能做 Y"的分层依赖

**收窄策略（仅适用于 comprehensive）：**
- 优先选择能跑通核心端到端路径的最小功能集
- 每个交付单元应可独立合并和验证
- 使用 AskUserQuestion 工具向用户展示收窄建议，先说明收窄原因和收窄后的范围，再提问确认

**路由：** 基于评估结果，读取对应的流程文件并完整执行其中的流程：
- 上述 express 信号 → 参见 `express.md`（与本文件同目录）
- 上述 comprehensive 信号 → 参见 `comprehensive.md`（与本文件同目录）

若信号不明确，使用 AskUserQuestion 向用户说明评估结果并确认路由方向。

## Key Principles

- **聚焦探索上下文** — 先 shadow 后源文件，仅读取与本次任务相关的文件，充分但不发散
- **聚焦已确认范围** — 评估和路由限定在实际需求范围内
- **忽略格式检查** — markdown lint 等文档格式告警一律忽略，内部规划文档不适用项目文档格式规范

## Anti-Pattern

- **"不读代码直接规划"** — 跳过上下文探索就评估任务，导致规划与现有实现脱节
