---
name: design
description: Use ONLY when the user explicitly invokes /vibewire:design. Do not auto-trigger based on the existence of architecture documents or planning artifacts.
---

# Design: From Architecture to Milestones

## Overview

接收编号参数，读取对应 spec 目录的需求文档和架构设计，将工作拆分为可独立交付的里程碑规划。

## Parameters

调用方式：`/vibewire:design {seq}`

- `{seq}`：spec 目录的编号（如 `001`、`002`）
- 根据 `{seq}` 在 `.vibewire/` 下匹配 `{seq}-*` 目录

<HARD-GATE>
在用户审阅并批准 design.md 之前，不要调用任何实现技能、编写任何代码或采取任何实现行动。
</HARD-GATE>

## Checklist

1. **Confirm Prerequisites** — 验证 project.md、requirements.md 和 architecture.md 存在且完整
2. **Build Context** — 读取项目文档、需求、架构、项目代码结构
3. **Analyze and Split Milestones** — 输出 design.md
4. **Self-Check** — 扫描占位符、覆盖度、合理性
5. **User Approval** — 请用户审阅 design.md
6. **Transition to Execution** — 提示用户使用 /vibewire:go

## Process

### 1. Confirm Prerequisites

- 扫描 `.vibewire/` 下匹配 `{seq}-*` 模式的目录，确定完整目录名 `{seq-name}`
- 若未找到匹配目录，提示用户可用的编号列表并中止
- 读取 `.vibewire/project.md`、`.vibewire/{seq-name}/requirements.md` 和 `.vibewire/{seq-name}/architecture.md`
- 确认文件内容完整，否则提示用户先运行 `/vibewire:intro`（缺少 project.md）或 `/vibewire:aim`（缺少 requirements/architecture）

### 2. Build Context

读取以下文档建立完整上下文：
- `.vibewire/project.md` — 项目全貌：当前架构、目录结构、技术栈、约定与规范
- `.vibewire/{seq-name}/requirements.md` — 需求范围和成功标准
- `.vibewire/{seq-name}/architecture.md` — 技术方案、模块划分、数据流
- `.shadow-api/` — 先读取影子 API 文件快速理解代码接口，再按需读取真实源文件深入细节
- 目标代码探索 — 根据需求和架构聚焦相关模块，不做无目的的全量扫描

### 3. Analyze and Split Milestones

将工作拆分为可独立交付的里程碑。

**规模控制：**
- 每个里程碑聚焦一个独立的能力维度，能用一句话描述完成后用户能做什么
- 超过 5 个模块变更的里程碑应考虑拆分——拆分后是否各自仍有独立价值？
- 仅涉及单一模块小改动的里程碑通常应合并到相邻里程碑，除非它有明确的独立交付价值

**分层策略：**
- **Milestone 1**：最小可行切片——能跑通核心端到端路径的最小功能集，让用户能尽早验证方向
- **Milestone 2+**：正交核心功能——每个里程碑聚焦一个独立的能力维度
- **后续里程碑**：扩展增强、性能优化、边界情况处理

**拆分原则：**
- 每个里程碑必须可独立合并和验证，不依赖后续里程碑才能运行
- 有明确的完成标准——可用一句话描述"这个里程碑完成后用户能做什么"
- 里程碑间依赖应单向线性，避免循环或菱形依赖
- 设计决策必须记录理由，不可省略"为什么"

**拆分检查：** 对每个里程碑回答：
1. 合并后用户能做什么？（验证独立价值）
2. 不做后续里程碑，功能是否可用？（验证独立性）
3. 是否包含不属于同一能力维度的功能？（验证内聚性）

将结果写入 `.vibewire/{seq-name}/design.md`，格式如下：

```markdown
# 实现总体设计

## 概述
{一句话总结整体实现方案}

## Milestone {N}-{名称}

### 目标
{这个里程碑完成后用户能做什么}

### 涉及模块
{列出涉及的模块/目录及变更类型：新增 | 修改 | 重构}

### 设计决策
{记录关键设计选择及其理由。若直接沿用 architecture.md 方案则注明"沿用架构设计"}

### 验证标准
{合并后如何验证功能正确：可运行的行为验证，非内部测试细节}
```

### 4. Self-Check

对照以下清单检查 design.md，发现问题直接修复：
1. **占位符扫描** — 是否有"TBD"、"TODO"、不完整部分？修复它们
2. **架构覆盖** — architecture.md 的每个需求/模块，能否指向一个具体的里程碑来实现？列出未覆盖的缺口并补充
3. **独立可交付** — 每个里程碑是否可独立合并和验证？
4. **依赖顺序** — 里程碑间的依赖顺序是否清晰合理？
5. **重复实现** — 跨里程碑是否有重复实现的功能？应提取为公共库

### 5. User Approval

请用户审阅 design.md，如用户要求修改，修改后重新自检再请用户审阅。仅在用户确认后继续。

### 6. Transition to Execution

提示用户下一步：

```
全局设计已完成！里程碑规划已保存到 .vibewire/{seq-name}/design.md。

下一步：
- 当前会话：直接运行 /vibewire:go 开始执行
- 新会话：在新会话中运行 /vibewire:go，系统会读取最新的规划文档
```

## Key Principles

- **最小可行切片优先** — Milestone 1 应尽可能小但仍可独立运行
- **独立可交付** — 每个里程碑可独立合并和验证
- **公共库识别** — 跨里程碑复用的功能提取为公共库
- **记录决策理由** — 每个设计决策都说明"为什么"
