# Express: Lightweight Task — Plan and Execute

## Overview

通过协作对话澄清需求，设计并执行实现方案。

<HARD-GATE>
在用户确认方案之前，不编写任何测试或实现代码。
</HARD-GATE>

## Checklist

你必须为以下每个项目创建任务并按顺序完成：
1. **Requirements Clarification** — 理解目的/约束/成功标准，确认方案
2. **TDD: Red** — 编写测试并确认失败
3. **TDD: Green** — 编写实现并确认通过
4. **Review** — 调用三个审查 subagent
5. **Fix** — 根据审查结果修复
6. **Update Shadow** — 更新 .shadow/ 声明文件
7. **Write Records** — 写入任务文档并追加执行记录
8. **Commit** — 提交全部变更

## Process

### 1. Requirements Clarification

使用 AskUserQuestion 工具逐一提问以完善需求：
- 每条消息只问一个问题
- 重点关注理解：目的、约束
- 充分理解后，呈现目标摘要供用户确认
- 用户确认后方可继续，如需修改则重新讨论

### 2. TDD: Red — Write Test

**铁律**：生产代码只在使失败测试通过时才编写。任何在失败测试之前写出的生产代码必须删除重写——不是回退补充测试，而是删除生产代码后从 Red 开始。

为当前任务编写测试：
- **行为而非实现** — 断言可观测的输入输出，不探测内部状态、私有方法或实现细节
- **隔离而非绕过** — mock 仅用于隔离外部依赖（网络、数据库、第三方服务），不用于跳过业务逻辑；mock 必须反映真实数据结构的完整形态
- **独立且确定** — 每个用例独立运行，不依赖执行顺序或共享可变状态
- **断言即证明** — 每条断言必须证明一个具体的行为事实；"没报错就是通过"不构成证明
- **完整覆盖** — 主动补充边界值、异常路径、空值和类型错误场景，不局限于快乐路径

**确认测试失败：** 运行测试，验证新增测试确实失败。若测试通过（未断言或断言了永真条件），修正测试后重新确认。

### 3. TDD: Green — Write Implementation

编写最小实现代码使测试通过：
- **遵循既有模式** — 不引入项目中不存在的新模式
- **最小化影响范围** — 只修改任务要求的部分

**验证通过：** 运行全量测试，循环修复直到全部通过。若修复后仍失败，且无法解释为何上一次修复应该生效，立即停止并向用户报告阻塞原因。

### 4. Review

同时启动三个审查 subagent（并行）：

```
subagent_type: "vibewire:quality-reviewer"
description: "quality-reviewer express"
prompt: |
  执行质量审查。
  模式：express
  任务目标：{一句话任务目标}
```

```
subagent_type: "vibewire:efficiency-reviewer"
description: "efficiency-reviewer express"
prompt: |
  执行效率审查。
  模式：express
  任务目标：{一句话任务目标}
```

```
subagent_type: "vibewire:reuse-reviewer"
description: "reuse-reviewer express"
prompt: |
  执行复用审查。
  模式：express
  任务目标：{一句话任务目标}
```

等待三个 agent 完成，汇总各自的 Status Report。

### 5. Fix

根据三个审查器 Status Report 中的发现进行修复：
- **Critical / Major** — 必须修复，修复后重跑全量测试确认通过
- **Minor / Info** — 自行判断是否修复

若审查无 Critical 或 Major 问题，跳过此步骤。

### 6. Update Shadow

为涉及的源代码文件更新 `.shadow/` 下的对应声明文件：
- 提取依赖引入语句（`import`、`require` 等）、函数签名、类（含全部属性和方法签名）、接口、类型、枚举、常量声明
- 省略所有函数体和初始化逻辑，保留原始注释
- 格式：仅顶层声明和类内部方法行尾追加 `// L{start}-{end}`，属性和字段不标注行号
- 增量更新：已存在的 shadow 文件仅更新变更声明，不存在则创建
- 排除非源码文件（`*.json`、`*.md`、`*.yaml`、`*.lock`、`*.test.*`、`*.spec.*`、`*.config.*`、`*.css`、`*.html` 等）
- 测试代码仅标记存在与行范围，不展开声明；若文件仅含测试代码，跳过该文件

### 7. Write Records

确定文档名称：`{name}` 为任务对应的英文标识，kebab-case（如 `fix-login-bug`）。

**Step A — 任务文档**

创建 `.vibewire/express/` 目录（若不存在），写入 `.vibewire/express/{name}.md`：

```markdown
# {name}

## 目标
{要做什么、为什么}

## 解决方案
{最终如何解决、变更了哪些文件、关键设计决策}

## Changes
- `path/to/file` (A/M/D) — {变更内容}
```

**Step B — 经验归纳**

若执行过程中（TDD 循环、审查发现、修复决策等）产生了值得记录的经验教训，归纳后追加到 `.vibewire/evolve.md`（如不存在则创建）。每条经验格式如下：

```markdown
## express/{name}

**{模式标题}**：{一句话描述}
- 根因：{为什么会发生}
- 建议：{后续如何避免}
```

归纳规则：
- 从执行过程中的发现提炼泛用模式，不逐条搬运原始观察
- 每条必须包含根因和建议，缺少任一项说明归纳不够深入
- 无值得归纳的经验时跳过此步骤

**Step C — 项目文档更新**

在 `.vibewire/CHANGELOG.md`（如不存在则创建）顶部追加变更条目：

```markdown
## yyyy-mm-dd | express/{name}
- 变更：{变更的模块/文件及原因}
```

对照当前 `.vibewire/project.md`，若本次任务影响了架构描述（新增/变更模块、技术栈变更、目录结构变化等），更新受影响的章节。首行元信息固定更新：`> Last updated: yyyy-mm-dd | express/{name}`。无影响则跳过。

### 8. Commit

```
git add {涉及的源代码文件} .shadow/ .vibewire/express/{name}.md .vibewire/evolve.md .vibewire/CHANGELOG.md .vibewire/project.md
git commit -m "[express/{name}] feat: {一句话描述}"
```

## Key Principles

- **聚焦已确认范围** — 不添加计划外功能
- **一次一个问题** — 不用多个问题淹没用户
- **利用已有上下文** — aim 已完成项目上下文探索，直接利用
- **忽略格式检查** — markdown lint 等文档格式告警一律忽略

## Anti-Pattern

- **"太简单不需要测试"** — 不存在例外
- **"先写代码再补测试"** — 必须删除代码后重做
- **"顺便重构一下"** — 聚焦当前任务，不夹带无关变更
- **"跳过审查直接提交"** — 即使轻量级任务，审查仍是必要环节
