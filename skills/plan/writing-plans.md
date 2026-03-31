---
name: writing-plans
description: "在有规格说明或需求需要多步骤实现时使用，在编写代码之前创建实现计划。"
---

# 编写实现计划

## 概述

编写详尽的实现计划，假设执行工程师对我们的代码库毫无了解，且品味存疑。记录他们需要知道的一切：每个任务要触碰哪些文件、代码、测试、可能需要查阅的文档，以及如何测试。将整个计划拆分为小颗粒度的任务。DRY（不重复）。YAGNI（不做不需要的）。TDD（测试驱动开发）。频繁提交。

假设他们是有经验的开发者，但对我们使用的工具链和问题领域知之甚少。假设他们不太了解良好的测试设计。

**开始时声明：** "我正在使用 writing-plans 技能来创建实现计划。"

**上下文：** 此技能应在专用工作树（worktree）中运行（由 brainstorming 技能创建）。

**计划保存路径：** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## 小颗粒度任务

**每个步骤是一个动作（2-5 分钟）：**
- "编写失败的测试" — 一个步骤
- "运行测试确认它失败" — 一个步骤
- "编写使测试通过的最小实现" — 一个步骤
- "运行测试确认它们通过" — 一个步骤
- "提交" — 一个步骤

## 计划文档头部

**每个计划必须以此头部开始：**

```markdown
# [功能名称] 实现计划

> **致 Claude：** 必需子技能：使用 superpowers:executing-plans 逐任务执行此计划。

**目标：** [一句话描述此功能构建什么]

**架构：** [2-3 句描述方案]

**技术栈：** [关键技术/库]

---
```

## 任务结构

````markdown
### 任务 N: [组件名称]

**文件：**
- 创建：`exact/path/to/file.py`
- 修改：`exact/path/to/existing.py:123-145`
- 测试：`tests/exact/path/to/test.py`

**步骤 1：编写失败的测试**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**步骤 2：运行测试确认失败**

运行：`pytest tests/path/test.py::test_name -v`
预期：失败，提示 "function not defined"

**步骤 3：编写最小实现**

```python
def function(input):
    return expected
```

**步骤 4：运行测试确认通过**

运行：`pytest tests/path/test.py::test_name -v`
预期：通过

**步骤 5：提交**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## 记住
- 始终给出精确的文件路径
- 计划中包含完整代码（不要只写"添加验证"）
- 精确的命令及预期输出
- 使用 @ 语法引用相关技能
- DRY、YAGNI、TDD、频繁提交

## 执行交接

保存计划后，提供执行选项：

**"计划已完成并保存到 `docs/plans/<filename>.md`。两种执行方式：

**1. 子代理驱动（当前会话）** — 每个任务派发新的子代理，任务之间进行代码审查，快速迭代

**2. 并行会话（独立会话）** — 在新会话中使用 executing-plans，带检查点的批量执行

**选择哪种方式？"**

**如果选择子代理驱动：**
- **必需子技能：** 使用 superpowers:subagent-driven-development
- 留在当前会话
- 每个任务使用新子代理 + 代码审查

**如果选择并行会话：**
- 引导用户在工作树中打开新会话
- **必需子技能：** 新会话使用 superpowers:executing-plans
