---
name: aim
description: Use ONLY when the user explicitly invokes /vibewire:aim. Do not auto-trigger based on codebase analysis, feature requests, or perceived implementation needs.
---

# Aim: From Task to Architecture

## Overview

通过协作对话将用户任务转化为 requirements.md + architecture.md。

<HARD-GATE>
在用户审阅并批准架构设计文档之前，不要调用任何实现技能、编写任何代码或采取任何实现行动。这适用于每个项目，无论感知的简单程度如何。
</HARD-GATE>

## Anti-Pattern: "This Is Too Simple to Plan"

每个项目都要经过这个过程。简单的功能、配置更改——所有这些。"简单"的项目是未审视的假设导致最多浪费工作的地方。规划可以很短（对于真正简单的项目只需几句话），但你必须呈现它并获得批准。

## Checklist

你必须为以下每个项目创建任务并按顺序完成：

1. **Explore Project Context** — 读取项目文档，若无则提示运行 /vibewire:intro
2. **Assess Task Scope** — 判断任务是否需要拆分为独立子项目
3. **Requirements Clarification** — 一次一个，理解目的/约束/成功标准
4. **Present Requirements** — 向用户展示完整需求描述
5. **Write and Self-Check Requirements** — 创建规划目录，写入 requirements.md，自检占位符/矛盾/歧义/范围
6. **Explore Solutions** — 附带权衡和你的建议
7. **Present Architecture Design** — 仅需求级增量：模块划分、数据流；项目级决策变更在 architecture.md 中标注
8. **Write and Self-Check Architecture** — 写入 architecture.md，自检
9. **User Review Architecture** — 请用户审阅架构文档文件
10. **Transition to Execution** — 总结对话，提示用户使用 /vibewire:design

## Process

### 1. Explore Project Context

**读取项目文档：** 读取 `.vibewire/project.md` 和 `.vibewire/CHANGELOG.md` 获取项目全貌。若不存在，提示用户先运行 `/vibewire:intro`。

### 2. Assess Task Scope

在深入需求澄清之前，评估任务规模：
- 如果任务描述了多个独立子系统（如"构建一个包含聊天、文件存储、计费和分析的平台"），立即标记
- 不要花大量问题去细化一个需要拆分的项目的细节
- 如果项目对单个规划来说太大，帮助用户拆分为子项目：哪些是独立的部分，它们如何关联，应该以什么顺序构建？然后对第一个子项目走正常的规划流程
- 每个子项目拥有自己的 aim→design→go 周期

### 3. Requirements Clarification

理解想法：
- 逐一提问以完善需求
- 尽可能使用选择题，但开放性问题也可以
- 每条消息只问一个问题 — 如果某个主题需要更多探索，将其拆分为多个问题
- 重点关注理解：目的、约束、成功标准

### 4. Present Requirements

一旦你理解了需求：
- 呈现需求详述给用户确认
- 涵盖：任务概述、功能需求、非功能需求、约束条件、成功标准

### 5. Write and Self-Check Requirements

获得用户确认后，创建规划目录并写入需求文档。

**创建规划目录：** `.vibewire/{seq-name}/`
- 扫描 `.vibewire/` 下已有的 `{seq-name}/` 目录，确定当前最大序号
- 序号：三位数字，在已有最大序号基础上递增（无已有目录则从 001 开始）
- 名称：任务对应的英文标识，kebab-case（如 `user-auth`）

写入 `.vibewire/{seq-name}/requirements.md`

**文档自检：**

写入后立即自检，无需重新审阅——修复后继续：
1. **占位符扫描** — 是否有"TBD"、"TODO"、不完整部分或模糊需求？修复它们
2. **内部一致性** — 各部分是否互相矛盾？需求是否与项目上下文一致？
3. **范围检查** — 是否聚焦于一个可实现的范围，还是需要拆分？
4. **歧义检查** — 是否有需求可以被两种不同方式解读？如有，选择一种并明确说明

### 6. Explore Solutions

提出2-3个不同的架构方案：
- 每个方案附带权衡分析
- 以对话方式呈现选项，附上你的建议和理由
- 首先提出你推荐的选项并解释原因
- 让用户选择或提出修改意见

### 7. Present Architecture Design

基于选定的方案呈现需求级架构设计。仅关注**本需求的架构增量**，项目级决策（技术栈、错误处理策略、测试策略）沿用 `project.md` 或在此提出变更。

**覆盖范围：**
- **架构概览** — 本需求在当前架构中的位置和整体方案
- **模块划分** — 新增/变更哪些模块，每个模块的单一职责
- **数据流** — 模块间的数据流转方向和通信模式

**不包含**：
- 技术栈、错误处理策略、测试策略 → `project.md`
- 接口签名、数据 schema
- 具体实现细节
- 代码结构、具体代码

按复杂度缩放每个部分：如果简单则几句话，如果复杂则更详细（最多200-300字）。每个部分后询问目前看起来是否正确。

**模块化设计：**
- 将系统拆分为更小的单元，每个单元有单一明确的用途，通过定义良好的接口通信，可独立理解和测试
- 对每个单元应能回答：它做什么，怎么使用，依赖什么？
- 能否在不阅读内部实现的情况下理解一个单元的用途？能否在不破坏消费者的情况下修改内部实现？如果不能，边界需要调整

**在现有代码库中工作：**
- 先探索现有结构，遵循既有模式
- 当现有代码存在影响当前工作的问题（如文件过大、边界不清、职责纠缠）时，将针对性改进作为设计的一部分
- 不要提出无关的重构，保持聚焦于当前目标

**项目级决策变更：**
如果本需求需要变更 `project.md` 中的项目级决策（如新增依赖、变更技术栈），在此一并提出并说明理由，获得用户确认。变更提案记录在 architecture.md 中，由 evolver 在里程碑执行后同步到 project.md。

### 8. Write and Self-Check Architecture

获得用户确认后，写入架构文档。

**写入 architecture.md：** 仅包含需求级架构增量（模块划分、数据流），以及 Step 7 中确认的项目级决策变更提案（标注为"待同步至 project.md"）。不含其他项目级信息。

**文档自检：**

与需求文档相同的自检流程——扫描占位符、内部一致性、范围、歧义。修复后继续。

### 9. User Review Architecture

架构文档写入并自检后，请用户审阅文档文件。如用户要求修改，修改后重新自检再请用户审阅。仅在用户确认后继续。

### 10. Transition to Execution

提示用户下一步：

```
规划已完成！需求文档和架构设计已保存到 .vibewire/{seq-name}/ 目录。

下一步：
- 当前会话：直接运行 /vibewire:design 进行全局设计
- 新会话：在新会话中运行 /vibewire:design，系统会读取最新的规划文档
```

## Key Principles

- **一次一个问题** — 不要用多个问题淹没用户
- **优先选择题** — 在可能的情况下比开放性问题更容易回答
- **严格执行YAGNI** — 从所有设计中删除不必要的功能
- **探索替代方案** — 在确定之前始终提出2-3个方案
- **增量验证** — 呈现设计，在继续之前获得批准
- **保持灵活** — 当某些内容不合理时返回去澄清
- **深度探索上下文** — 充分了解项目现状再做规划
- **模块化设计** — 系统应拆分为边界清晰、可独立理解的单元
