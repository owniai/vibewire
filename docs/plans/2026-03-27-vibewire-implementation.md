# Vibewire Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 构建一个Claude Code插件，实现Agent完全自主的端到端开发流程

**Architecture:** 主控Skill负责调度流程，6个专业Agent各司其职，两层迭代模型（阶段+批次），过程资料统一存放在.vibewire目录

**Tech Stack:** Claude Code Plugin (SKILL.md + Agent markdown)

---

## Phase 1: 项目基础结构

### Task 1.1: 创建插件元数据
**Files:**
- Create: `.claude-plugin/plugin.json`

**Actions:**
1. 创建 `.claude-plugin/` 目录
2. 创建 `plugin.json`，包含name、version、description、keywords

### Task 1.2: 创建hooks预留结构
**Files:**
- Create: `hooks/hooks.json`

**Actions:**
1. 创建 `hooks/` 目录
2. 创建空的 `hooks.json` 配置文件

### Task 1.3: 创建项目README
**Files:**
- Create: `README.md`

**Actions:**
1. 创建README，包含项目介绍、使用方式、目录结构说明

---

## Phase 2: 六个专业Agent

### Task 2.1: 创建 requirement-analyzer Agent
**Files:**
- Create: `agents/requirement-analyzer.md`

**Actions:**
1. 创建 `agents/` 目录
2. 定义frontmatter（name, description, model, effort）
3. 编写系统提示词：职责、输入输出格式、工作流程

### Task 2.2: 创建 architect Agent
**Files:**
- Create: `agents/architect.md`

**Actions:**
1. 定义frontmatter
2. 编写系统提示词：技术方案设计、架构输出格式

### Task 2.3: 创建 planner Agent
**Files:**
- Create: `agents/planner.md`

**Actions:**
1. 定义frontmatter
2. 编写系统提示词：阶段划分逻辑、任务批次规划、输出格式

### Task 2.4: 创建 implementer Agent
**Files:**
- Create: `agents/implementer.md`

**Actions:**
1. 定义frontmatter
2. 编写系统提示词：代码实现规范、TDD要求、输出格式

### Task 2.5: 创建 tester Agent
**Files:**
- Create: `agents/tester.md`

**Actions:**
1. 定义frontmatter
2. 编写系统提示词：测试编写规范、验证逻辑、输出格式

### Task 2.6: 创建 reviewer Agent
**Files:**
- Create: `agents/reviewer.md`

**Actions:**
1. 定义frontmatter
2. 编写系统提示词：审查标准、问题分类、输出格式

---

## Phase 3: 主控Skill

### Task 3.1: 创建主控Skill框架
**Files:**
- Create: `skills/go/SKILL.md`

**Actions:**
1. 创建 `skills/go/` 目录
2. 定义frontmatter（name, description）
3. 编写Skill概述和核心原则

### Task 3.2: 实现初始化逻辑
**Files:**
- Modify: `skills/go/SKILL.md`

**Actions:**
1. 编写初始化步骤：创建 `.vibewire/` 目录
2. 定义任务信息记录格式
3. 定义状态管理机制

### Task 3.3: 实现前期准备阶段
**Files:**
- Modify: `skills/go/SKILL.md`

**Actions:**
1. 编写调用 requirement-analyzer 的逻辑
2. 编写调用 architect 的逻辑
3. 定义输出文件路径和格式要求

### Task 3.4: 实现阶段规划逻辑
**Files:**
- Modify: `skills/go/SKILL.md`

**Actions:**
1. 编写调用 planner 获取阶段列表的逻辑
2. 定义阶段元数据格式

### Task 3.5: 实现阶段循环逻辑
**Files:**
- Modify: `skills/go/SKILL.md`

**Actions:**
1. 编写阶段循环框架
2. 编写调用 planner 获取 design.md + tasks.md 的逻辑
3. 定义阶段目录命名规则

### Task 3.6: 实现批次循环逻辑
**Files:**
- Modify: `skills/go/SKILL.md`

**Actions:**
1. 编写批次循环框架
2. 编写调用 implementer 的逻辑
3. 编写调用 tester 的逻辑
4. 编写调用 reviewer 的逻辑

### Task 3.7: 实现决策判断逻辑
**Files:**
- Modify: `skills/go/SKILL.md`

**Actions:**
1. 编写批次通过/失败的判断逻辑
2. 编写文档调整触发条件
3. 编写重试机制（最多3次）

### Task 3.8: 实现错误处理
**Files:**
- Modify: `skills/go/SKILL.md`

**Actions:**
1. 编写Agent调用超时处理
2. 编写文件写入失败处理
3. 编写连续失败暂停机制

### Task 3.9: 实现完成逻辑
**Files:**
- Modify: `skills/go/SKILL.md`

**Actions:**
1. 编写阶段总结生成逻辑
2. 编写最终总结生成逻辑
3. 编写完成通知

---

## Phase 4: 验证与测试

### Task 4.1: 创建测试用例
**Files:**
- Create: `docs/test-cases.md`

**Actions:**
1. 定义简单测试任务（如：实现一个hello world函数）
2. 定义中等测试任务（如：实现一个计算器）
3. 记录预期输出

### Task 4.2: 手动验证完整流程
**Actions:**
1. 安装插件到Claude Code
2. 运行简单测试任务
3. 验证 `.vibewire/` 目录结构正确
4. 验证各阶段文件生成正确

### Task 4.3: 修复发现的问题
**Actions:**
1. 记录运行中发现的问题
2. 修复Agent提示词问题
3. 修复主控Skill逻辑问题

---

## Phase 5: 文档完善

### Task 5.1: 完善README
**Files:**
- Modify: `README.md`

**Actions:**
1. 添加安装说明
2. 添加使用示例
3. 添加目录结构说明
4. 添加配置说明

### Task 5.2: 创建CHANGELOG
**Files:**
- Create: `CHANGELOG.md`

**Actions:**
1. 记录v0.1.0版本内容

---

## 任务依赖关系

```
Phase 1 (基础结构)
    ↓
Phase 2 (Agents) ─────────────────┐
    ↓                              │
Phase 3 (主控Skill) ←──────────────┘
    ↓
Phase 4 (验证测试)
    ↓
Phase 5 (文档完善)
```

---

## 估算

| Phase | 任务数 | 说明 |
|-------|--------|------|
| Phase 1 | 3 | 基础结构，快速完成 |
| Phase 2 | 6 | 6个Agent定义 |
| Phase 3 | 9 | 主控Skill核心逻辑 |
| Phase 4 | 3 | 验证测试 |
| Phase 5 | 2 | 文档完善 |
| **Total** | **23** | |
