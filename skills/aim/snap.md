# Snap: Small Task — Plan and Execute

## Overview

方案探索与 TDD 实现，更新 shadow 与项目文档。

<HARD-GATE>
在用户确认方案之前，不编写任何测试或实现代码。
</HARD-GATE>

## Checklist

你必须为以下每个项目创建任务并按顺序完成：
1. **Design Exploration** — 理解当前实现，探索方案，消除技术不确定性
2. **TDD: Red** — 编写测试并确认失败
3. **TDD: Green** — 编写实现并确认通过
4. **Write Records** — 写入任务文档并更新 CHANGELOG
5. **Update Shadow** — 更新 .shadow/ 声明文件
6. **Commit** — 提交变更

## Process

### 1. Design Exploration

aim 已完成项目上下文探索和需求澄清。本阶段聚焦 how——理解当前实现并确定解决方案。

**探索当前实现：**
- 定位并阅读需要修改的源文件，理解现有逻辑和模式
- 确认变更的影响范围和涉及的模块边界

**技术验证（按需）：** 若实现方案依赖未确认的技术事实（依赖兼容性、API 行为、数据格式等），执行针对性的快速验证。

- **技术调研** — 调用 scout agent：
  ```
  subagent_type: "vibewire:scout"
  description: "scout {调研目标}"
  prompt: |
    task-id：SNAP-{name}
    调研目标：{具体目标}
  ```

- **技术实验** — 调用 experimenter agent：
  ```
  subagent_type: "vibewire:experimenter"
  description: "experimenter {实验目标}"
  prompt: |
    task-id：SNAP-{name}
    实验目标：{具体目标}，意图：{要验证什么}
  ```

等待 agent 完成后，读取其 Status Report 中返回的文件路径，基于结论调整实现方案。

**确认方案：** 向用户展示实现方案和关键决策（包含技术验证的结论），获得确认后进入 TDD。

### 2. TDD: Red — Write Test

**铁律**：生产代码只在使失败测试通过时才编写。任何在失败测试之前写出的生产代码必须删除重写。

为当前任务编写测试：
- **行为而非实现** — 断言可观测的输入输出，不探测内部状态
- **独立且确定** — 每个用例独立运行，不依赖执行顺序或共享可变状态
- **断言即证明** — 每条断言必须证明一个具体的行为事实

**确认测试失败：** 运行测试，验证新增测试确实失败。

### 3. TDD: Green — Write Implementation

编写最小实现代码使测试通过：
- **遵循既有模式** — 不引入项目中不存在的新模式
- **最小化影响范围** — 只修改任务要求的部分

**验证通过：** 运行全量测试，循环修复直到全部通过。

### 4. Write Records

确定文档名称：`{name}` 为任务对应的英文标识，kebab-case（如 `fix-login-bug`）。

#### 4.1 Action Record

创建 `.vibewire/actions/` 目录（若不存在），写入 `.vibewire/actions/{name}.md`：

```markdown
# {name}

## 目标
{要做什么、为什么}

## 解决方案
{最终如何解决、变更了哪些文件}

## Changes
- `path/to/file` (A/M/D) — {变更内容}
```

#### 4.2 Lessons Synthesis

若任务产生了值得沉淀的知识——bug 的根因与防御手段、隐含假设及需满足的前提、非显而易见的设计约束、正确的构建/测试/部署命令（尤其是试错后才发现的）、必需的环境变量或前置步骤、必须遵守的执行顺序、执行过程中的发现——归纳后追加到 `.vibewire/evolve.md`（如不存在则创建）。每条经验格式如下：

```markdown
## SNAP-{name}

**{模式标题}**：{一句话描述}
- 根因：{为什么会发生}
- 建议：{后续如何避免}
```

归纳指引：
- 从具体发现提炼泛用模式，不逐条搬运原始观察
- 每条必须包含根因和建议，缺少任一项说明归纳不够深入
- 无值得归纳的经验时跳过此步骤

**Health Dashboard 联动**：若 `.vibewire/evolve.md` 顶部的 Health Dashboard 节已存在，更新与本次经验同一类别的条目；不新增信号条目。

#### 4.3 Changelog

在 `.vibewire/CHANGELOG.md`（如不存在则创建）顶部追加变更条目：

```markdown
## yyyy-mm-dd | SNAP-{name}
- 变更：{变更的模块/文件及原因}
```

#### 4.4 Project Update

对照当前 `.vibewire/project.md`，若本次任务影响了目录结构、技术栈、架构描述或产生了新的约定与规范，更新受影响的章节。首行元信息固定更新：`> Last updated: yyyy-mm-dd | SNAP-{name}`。无影响则跳过。

### 5. Update Shadow

调用 shadow-writer 更新变更文件的 shadow：

```
subagent_type: "vibewire:shadow-writer"
description: "shadow-writer SNAP-{name}"
prompt: |
  {变更文件列表，每行一个路径，删除文件前缀 DEL:}
```

### 6. Commit

```
git add {涉及的源代码文件} .shadow/ .vibewire/actions/{name}.md .vibewire/evolve.md .vibewire/CHANGELOG.md .vibewire/project.md
git commit -m "[SNAP-{name}] feat: {一句话描述}"
```

## Key Principles

- **聚焦已确认范围** — 不添加计划外功能
- **利用已有上下文** — aim 已完成项目上下文探索，直接利用
- **忽略格式检查** — markdown lint 等文档格式告警一律忽略

## Anti-Pattern

- **"太简单不需要测试"** — 不存在例外
- **"先写代码再补测试"** — 必须删除代码后重做
- **"顺手多做一点"** — micro 任务严格限定在单一目标内，额外变更应重新路由
- **"跳过设计探索直接写代码"** — 即使实现看起来直截了当，也需先确认当前代码的行为和变更影响范围。技术不确定性未消除就进入 TDD，常导致返工
