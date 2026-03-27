# Vibewire - Agent自主开发流程插件设计文档

## 1. 概述

### 1.1 项目目标

创建一套Agent自主开发流程，让Agent能够完全自主地从用户任务描述到最终可测试代码的全流程开发，无需用户中途介入。

### 1.2 核心特性

- **完全自主**：用户只需输入任务描述，全程无介入，最后验收结果
- **两层迭代**：阶段迭代 + 任务批次迭代
- **过程可追溯**：所有过程资料保存在 `.vibewire/` 目录

### 1.3 V1目标

最小可运行版本，能完成简单任务的端到端流程。

---

## 2. 整体架构

### 2.1 系统架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                        用户输入任务                              │
│                    "/go 实现用户登录功能"                         │
└─────────────────────────┬───────────────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                     skills/go/SKILL.md                          │
│                      【主控调度器】                               │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │  职责：                                                       ││
│  │  1. 解析用户任务                                              ││
│  │  2. 阶段划分与状态管理                                         ││
│  │  3. 调度对应Agent执行                                         ││
│  │  4. 收集结果、判断是否进入下一阶段                              ││
│  │  5. 两层迭代控制（阶段迭代 + 任务批次迭代）                      ││
│  └─────────────────────────────────────────────────────────────┘│
└─────────────────────────┬───────────────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                      agents/ (6个Agent)                         │
├────────────────┬────────────────┬───────────────────────────────┤
│ requirement-   │   architect    │      planner                  │
│   analyzer     │                │                               │
│  需求分析       │   架构设计      │     计划制定                   │
├────────────────┼────────────────┼───────────────────────────────┤
│  implementer   │    tester      │      reviewer                 │
│   代码实现      │     测试        │     代码审查                   │
└────────────────┴────────────────┴───────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                    .vibewire/ (过程资料)                         │
└─────────────────────────────────────────────────────────────────┘
```

### 2.2 项目目录结构

```
vibewire/
├── .claude-plugin/
│   └── plugin.json          # 插件元数据
│
├── skills/
│   └── go/                  # 主控skill
│       └── SKILL.md
│
├── agents/                  # 6个专业Agent
│   ├── requirement-analyzer.md
│   ├── architect.md
│   ├── planner.md
│   ├── implementer.md
│   ├── tester.md
│   └── reviewer.md
│
├── hooks/
│   └── hooks.json           # 预留
│
└── README.md
```

---

## 3. 过程资料目录结构

### 3.1 .vibewire/ 目录

```
.vibewire/
├── requirements.md            # 需求分析文档（前期）
├── architecture.md            # 架构设计文档（前期）
│
├── stage-1-xxx/               # 执行阶段1（名称由planner根据任务内容命名）
│   ├── design.md              # 该阶段详细设计
│   ├── tasks.md               # 任务批次列表
│   ├── batch-1/
│   │   ├── implementation.md
│   │   ├── test-results.md
│   │   └── review-results.md
│   ├── batch-2/
│   │   └── ...
│   └── summary.md             # 阶段总结
│
├── stage-2-yyy/               # 执行阶段2
│   ├── design.md
│   ├── tasks.md
│   ├── batch-1/
│   │   └── ...
│   └── summary.md
│
├── stage-N-zzz/               # 执行阶段N（动态数量）
│   └── ...
│
└── final-summary.md           # 最终总结
```

---

## 4. 执行流程

### 4.1 流程概览

```
【前期准备】
  ├─ requirement-analyzer → requirements.md
  └─ architect → architecture.md

【阶段规划】
  └─ planner 分析任务，划分执行阶段（如：基础框架 → 核心功能 → 扩展功能 → 收尾）

【执行阶段1】
  └─ planner → stage-1/design.md + tasks.md
  └─ 批次迭代: implementer → tester → reviewer → (问题则调整文档)

【执行阶段2】
  └─ planner → stage-2/design.md + tasks.md
  └─ 批次迭代: implementer → tester → reviewer

...

【执行阶段N】
  └─ planner → stage-N/design.md + tasks.md
  └─ 批次迭代: implementer → tester → reviewer

【完成】
  └─ 生成 final-summary.md
```

### 4.2 主控Skill核心逻辑

```
用户输入任务
    ↓
【1. 初始化】
    ├─ 创建 .vibewire/ 目录
    └─ 记录任务信息
    ↓
【2. 前期准备】
    ├─ 调用 requirement-analyzer → requirements.md
    └─ 调用 architect → architecture.md
    ↓
【3. 阶段规划】
    └─ 调用 planner → 输出阶段列表
    ↓
【4. 阶段循环】
    └─ 对每个执行阶段:
        ├─ 调用 planner → stage-N/design.md + tasks.md
        └─ 【批次循环】
            └─ 对每个批次:
                ├─ 调用 implementer
                ├─ 调用 tester
                ├─ 调用 reviewer
                └─ 判断审查结果:
                    ├─ 通过 → 下一批次
                    └─ 有问题 → 调整文档 → 重新执行批次
        └─ 生成 stage-N/summary.md
    ↓
【5. 完成】
    └─ 生成 final-summary.md
```

---

## 5. Agent职责

### 5.1 Agent清单

| Agent | 职责 | 输入 | 输出 |
|-------|------|------|------|
| **requirement-analyzer** | 分析用户任务，提取需求点，澄清边界 | 用户任务描述 | requirements.md |
| **architect** | 设计技术方案，确定架构、技术栈、模块划分 | requirements.md | architecture.md |
| **planner** | 划分执行阶段和任务批次，制定每阶段详细计划 | architecture.md | stage-N/design.md + tasks.md |
| **implementer** | 按批次执行代码实现 | design.md + tasks.md | 代码文件 + implementation.md |
| **tester** | 编写/执行测试，验证实现是否符合设计 | 代码 + design.md | test-results.md |
| **reviewer** | 审查代码质量和规范性，检查是否偏离设计 | 代码 + design.md + test-results | review-results.md |

### 5.2 Agent协作关系

```
requirement-analyzer → architect → planner
                                   ↓
                          ┌────────────────┐
                          │  循环：每阶段   │
                          │  ↓             │
                          │  planner       │
                          │  ↓             │
                          │  ┌───────────┐ │
                          │  │循环：每批次│ │
                          │  │  ↓        │ │
                          │  │implementer│ │
                          │  │  ↓        │ │
                          │  │  tester   │ │
                          │  │  ↓        │ │
                          │  │ reviewer  │ │
                          │  │  ↓        │ │
                          │  │有问题？   │ │
                          │  │  是→调整  │ │
                          │  │  否→继续  │ │
                          │  └───────────┘ │
                          └────────────────┘
```

---

## 6. 关键决策点

### 6.1 判断逻辑

| 决策点 | 判断逻辑 |
|--------|----------|
| 批次是否通过 | reviewer 输出中无阻断性问题 |
| 是否需要调整文档 | reviewer 发现设计与实现不一致 |
| 阶段是否完成 | 所有批次通过 + summary.md 已生成 |
| 整体是否完成 | 所有阶段完成 + final-summary.md 已生成 |

### 6.2 错误处理

| 场景 | 处理方式 |
|------|----------|
| 批次连续失败3次 | 暂停，记录问题，等待用户介入 |
| Agent调用超时 | 重试1次，失败则标记错误继续下一阶段 |
| 文件写入失败 | 记录日志，尝试备用路径 |

---

## 7. Plugin配置

### 7.1 plugin.json

```json
{
  "name": "vibewire",
  "version": "0.1.0",
  "description": "Agent自主开发流程插件 - 完全自动化从需求到代码的端到端开发",
  "keywords": ["autonomous", "development", "agent", "workflow"]
}
```

### 7.2 使用方式

```bash
# 用户在任意项目中使用
/go 实现一个用户登录功能，支持邮箱密码和OAuth登录
```

系统自动：
1. 在当前目录创建 `.vibewire/` 存放过程资料
2. 按流程执行各个阶段
3. 最终输出代码 + 测试 + 文档

---

## 8. 设计决策记录

| 决策项 | 选择 | 理由 |
|--------|------|------|
| 输出范围 | 设计文档 + 实施计划 + 代码 + 测试 | 完整可交付物 |
| 系统类型 | 全新构建 | 独立可控 |
| Agent数量 | 6个 | 职责清晰，覆盖全流程 |
| 迭代模型 | 两层迭代 | 阶段迭代 + 批次迭代，灵活可控 |
| 用户介入 | 完全自主 | V1目标是最小可运行 |
| 过程资料位置 | .vibewire/ | 不污染用户项目 |
| 阶段划分 | 动态 | 根据任务复杂度自适应 |
