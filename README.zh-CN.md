# VibeWire

**中文** | [English](README.md)

Claude Code 的自主开发工作流插件。从任务描述到测试通过、审查完成的代码——无需人工干预。

VibeWire 编排一组专业化的 Agent 流水线，代表你完成规划、实现、审查和迭代。你描述想要什么，Agent 负责怎么做。

---

## 工作原理

首先运行 `/vibewire:intro` 扫描代码库，建立文档基线（每个项目运行一次）。

根据任务选择合适的 skill：

- **Aim** — 适用于探索、研究、讨论和架构规划。Aim 澄清需求、调查未知项，必要时设计架构并生成分阶段交付计划。当任务涉及新技术或未验证的假设时，aim 派遣 **scout** 调查技术事实，派遣 **experimenter** 运行真实实验——将架构决策建立在已验证的数据之上。如果需要编码，aim 转向 snap 或产出 PLAN 文档供 go 使用。
- **Snap** — 适用于明确的实现任务。Snap 完成整个周期：分解 → TDD → 验证 → 可选审查 → 记录 → 提交。当变更涉及 3+ 文件或影响公共 API 时推荐进行审查。

对于产出 PLAN 的 aim 任务，运行 `/vibewire:go`。go 命令通过逐阶段流水线调度 Agent：

1. 对每个阶段，**implementer** 阅读架构、加载原子变更、以严格 TDD 执行
2. 三个审查者 — **efficiency**、**quality**、**reuse** — 并行检查结果
3. 如果发现 Critical/Major 级别问题，**resolver** 整合发现并应用最小修复
4. 所有阶段完成后，**acceptor** 针对架构进行完整验收验证
5. 如果发现问题，**fixer** 进入修复循环（最多 3 轮）
6. **evolver** 分析健康模式、提炼经验教训、更新项目文档

每个阶段是自包含的循环：实现 → 审查 → 修复。如果 implementer 被阻塞，会自动重试（最多两次后升级给你）。所有阶段完成后，验收验证确保需求在合并前得到满足。

所有过程产物存放在项目内的 `.vibewire/` 目录——架构、阶段设计、实现记录、审查报告和经验日志。一切透明可追溯。

---

## 安装

### Claude Code（通过插件市场）

先注册市场，然后安装插件：

```bash
claude plugins marketplace add https://github.com/owniai/vibewire
claude plugins install vibewire@vibewire
```

---

## 快速开始

```bash
# 第 1 步：扫描项目（运行一次）
/vibewire:intro

# 第 2 步：根据任务选择合适的 skill
/vibewire:aim   # 探索、澄清、按需架构规划 → /vibewire:go
/vibewire:snap  # 直接实现：分解 → TDD → 验证 → 可选审查 → 提交

# 第 3 步（仅 aim）: 自主执行计划
/vibewire:go PLAN-{N}-{name}
```

---

## 工作流

| 命令 / Skill | 用途 | 何时使用 |
|-------|------|----------|
| `/vibewire:intro` | 扫描项目，建立文档基线 | 每个项目一次，或代码库发生重大变化时 |
| `/vibewire:aim` | 探索、澄清和架构规划 | 需要在编码前探索、分析、决策或规划时 |
| `/vibewire:snap` | TDD 实现，可选审查 | 知道要构建什么，直接编码时 |
| `/vibewire:go` | 自主逐阶段实现，含审查循环 | aim 产出架构之后 |

```
/vibewire:intro → .vibewire/project.md, .vibewire/CHANGELOG.md

/vibewire:aim（探索与规划）:
  → 定位（读取项目上下文）
  → 澄清（what、why、how）
  → 研究 / 讨论 / 运维 / 文档
  → （可选）.vibewire/aims/AIM-{N}-{name}.md
  → 如需架构规划:
      → 探索架构（逐层确认）
      → scout（如有技术未知项）→ .vibewire/tech-research/{task-id}.md + .vibewire/tech-research/knowledge.md
      → experimenter（如有未验证假设）→ .vibewire/experiments/{task-id}/
      → .vibewire/plans/PLAN-{N}-{name}/architecture.md
      → （用户审查并批准）
  → 如代码变更简单可转入 snap

/vibewire:snap（实现）:
  → 分解 → 确认
  → TDD（red → green）逐个原子变更
  → 验证（完整测试套件）
  → 审查决策（始终自审；可选 3 个审查者并行）
  → 修复（去重发现，修复或跳过）
  → .vibewire/CHANGELOG.md + .vibewire/evolve.md
  → commit

/vibewire:go PLAN-{N}-{name}
  → 创建 feature 分支
  → 逐阶段执行:
      implementer → 代码 + 测试（TDD）
      3 个审查者 → 审查报告
      resolver → 修复（如有 Critical/Major）
  → acceptor → 验收验证
  → fixer → 修复循环（如 FAIL，最多 3 轮）
  → evolver → 经验日志 + 项目文档更新
  → （用户选择合并策略）
```

---

## 内容概览

### Commands（2 个）

| Command | 描述 |
|---------|------|
| **intro** | 扫描代码库，建立文档基线（`.vibewire/project.md`、`.vibewire/CHANGELOG.md`）。五步：确认范围 → 探索 → 写文档 → 审查 → 提交 |
| **go** | 迭代式分阶段交付。按实现、审查、验收和收尾阶段顺序调度 Agent |

### Skills（2 个）

| Skill | 描述 |
|-------|------|
| **aim** | 探索、澄清和架构规划：定位 → 澄清 → 研究 / 讨论 / 架构设计。产出 PLAN 文档供 /vibewire:go 使用 |
| **snap** | TDD 实现，可选审查：分解 → 实现 → 验证 → 审查决策 → 记录 → 提交 |

### Agents（10 个）

| Agent | 职责 | 使用方 |
|-------|------|--------|
| **scout** | 调查指定技术和依赖，产出事实性发现——版本、兼容性、约束。接收研究目标，不做决策 | aim |
| **experimenter** | 运行指定实验，获取真实结构、API 行为或性能数据。接收实验目标，不做决策 | aim |
| **implementer** | 阅读架构上下文，评估偏差，从阶段计划加载原子变更，逐任务 TDD——写测试、写代码、验证、修复 | go |
| **efficiency-reviewer** | 审查性能问题——不必要的计算、错失的并发、内存泄漏、算法低效 | snap, go |
| **quality-reviewer** | 审查反模式——冗余状态、参数膨胀、复制粘贴变体、过度抽象、代码异味 | snap, go |
| **reuse-reviewer** | 审查重复代码——搜索已有工具函数和模式，识别可复用的代码机会 | snap, go |
| **resolver** | 整合三个审查者的报告，去重发现，交叉验证问题，执行最小修复 | go |
| **acceptor** | 实现后验收——通过对抗性分析验证需求可追溯性，搜寻隐藏缺陷 | go |
| **fixer** | 修复验收中发现的问题和部分需求。测试驱动，最小变更 | go |
| **evolver** | 从审查/裁决数据中分析项目健康模式，维护 evolve.md 健康面板和经验记录，更新项目文档 | go |

---

## 架构

### 过程产物

所有过程产物存放在目标项目的 `.vibewire/` 目录：

```
.vibewire/
├── project.md                          # 项目概览（由 intro 创建）
├── CHANGELOG.md                        # 变更日志（由 intro 创建）
├── evolve.md                           # 跨里程碑经验和健康面板（由 evolver 创建）
├── tech-research/                      # 技术调研产物（由 scout 创建）
│   ├── knowledge.md                    # 全局调研知识库
│   └── {task-id}.md                    # 每个任务的详细调研
├── experiments/
│   ├── framework.md                    # 全局实验框架（由 experimenter 创建）
│   └── {task-id}/                      # 实验结果（由 experimenter 创建）
│       └── result.md
├── actions/                            # Action 和 Plan 记录
│   ├── {name}.md                       # Action 摘要（由 snap 创建）
│   └── PLAN-{N}-{name}/               # 每个 plan 任务的规划目录
│       ├── architecture.md             # 架构设计含 Stage Plan（由 aim 创建）
│       ├── log.md                      # 执行日志（由 implementer 创建）
│       ├── lessons.md                  # 累积经验（由 implementer、resolver、fixer 创建）
│       ├── review-efficiency.md        # 效率审查报告
│       ├── review-quality.md           # 质量审查报告
│       ├── review-reuse.md             # 复用审查报告
│       ├── resolve.md                  # 审查裁决记录（由 resolver 创建）
│       ├── acceptance.md               # 验收报告（由 acceptor 创建）
│       └── acceptance-{round}.md       # 归档的验收报告（由 fixer 创建）
└── aims/                               # Aim 记录（由 aim 创建）
    └── AIM-{N}-{name}.md             # 分析/研究结论
```

### Agent 流水线

```mermaid
flowchart TD
    subgraph aim["/vibewire:aim"]
        direction TB
        A0[用户描述任务] --> A1[定位 + 澄清]
        A1 --> A2{需要代码？}
        A2 -- 否 --> A3["研究 / 讨论 / 运维 / 文档"]
        A2 -- 是 --> A4{复杂还是简单？}
        A4 -- 复杂，需要规划 --> A5{有技术未知项？}
        A4 -- 简单，HOW 明确 --> snapflow

        A5 -- 是 --> A6["scout
        技术调查"]
        A6 --> A7{有未验证假设？}
        A5 -- 否 --> A7
        A7 -- 是 --> A8["experimenter
        运行真实实验"]
        A7 -- 否 --> A9[架构设计]
        A8 --> A9
    end

    subgraph snapflow["/vibewire:snap"]
        direction TB
        S0[用户确认范围] --> S1["分解 → TDD → 验证"]
        S1 --> S2{推荐审查？}
        S2 -- 是 --> S3["3 个审查者（并行）
        → 按需修复"]
        S2 -- 否 --> S4[记录 → 提交]
        S3 --> S4
    end

    subgraph go["/vibewire:go"]
        direction TB
        G0[用户批准] --> G1["implementer
        TDD 实现"]
        G1 --> G2{审查}

        G2 --> G3["efficiency-reviewer"]
        G2 --> G4["quality-reviewer"]
        G2 --> G5["reuse-reviewer"]

        G3 --> G6{有 Critical/Major
        问题？}
        G4 --> G6
        G5 --> G6
        G6 -- 是 --> G7["resolver
        整合并修复"]
        G6 -- 否 --> G8{还有更多阶段？}
        G7 --> G8
        G8 -- 是 --> G1
        G8 -- 否 --> G9["acceptor
        验收验证"]
        G9 -- PASS --> G10["evolver
        经验提炼"]
        G9 -- FAIL --> G11["fixer
        修复循环（最多 3 轮）"]
        G11 --> G9
        G11 -- 超过 3 轮 --> G12[用户介入]
        G10 --> G13[用户选择合并策略]
    end

    A9 --> go
```

---

## 设计理念

- **默认自主** — 你批准设计，Agent 处理其余一切。
- **合并前审查** — 三个独立审查者捕获不同类别的问题。Acceptor 在合并前验证每项架构需求。没有未经审查和验收的代码上线。
- **过程可追溯** — 每个决策、每次变更、每份审查都记录在 `.vibewire/` 中。
- **修复而非跳过** — 被阻塞的 Agent 触发自动返工。问题会被升级，不会被忽略。
- **范围自律** — aim 技能会对过大的任务提出质疑，帮助你优先交付最小的可用单元。

---

## 更新

```bash
claude plugins update vibewire@vibewire
```

---

## 致谢

VibeWire 受到了 Jesse Vincent 的 [Superpowers](https://github.com/obra/superpowers) 项目启发。以下模式和概念改编自 Superpowers：

- **Skill 格式** — 使用 YAML frontmatter 元数据和结构化章节来描述流程、原则和反模式的 Markdown 格式
- **设计先行工作流** — 在任何实现开始之前要求用户批准设计文档

## 许可证

MIT License - 详见 LICENSE 文件。
