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

### 2. Assess Task Scope & Route

判断任务应走 express 还是 comprehensive 流程。

**走 express — 小型任务：**
- bug 修复、配置调整、单个函数或模块内部逻辑变更
- 实现简单功能，变更范围可一眼估清
- 不涉及多模块协调或跨层依赖

**走 comprehensive — 常规任务：**
- 新增完整功能或重构涉及多个模块
- 存在可独立交付的中间节点或分层依赖
- 需要设计、拆分、分阶段交付

**路由：** 基于信号匹配，读取并执行对应的流程文件（与本文件同目录）：
- express 信号 → `express.md`
- comprehensive 信号 → `comprehensive.md`

信号不明确时，使用 AskUserQuestion 向用户说明评估结果并确认路由方向。

## Key Principles

- **聚焦探索上下文** — 先 shadow 后源文件，仅读取与本次任务相关的文件，充分但不发散
- **聚焦已确认范围** — 评估和路由限定在实际需求范围内
- **忽略格式检查** — markdown lint 等文档格式告警一律忽略，内部规划文档不适用项目文档格式规范

## Anti-Pattern

- **"不读代码直接规划"** — 跳过上下文探索就评估任务，导致规划与现有实现脱节
