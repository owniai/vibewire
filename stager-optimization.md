# stager.md 优化方向评估报告

> 评估对象：`agents/stager.md`
> 参考资料目录：`ref/everything-claude-code/agents/`
> 评估日期：2026-04-01

---

## 一、结构与指令设计（评估方向 1）

### 1.1 [P0] 动作拆分：`milestone` 承载过多职责

**现状**：`milestone` 动作同时负责里程碑设计 + 阶段拆分，占文档约 60%。

**问题**：两种输出粒度和复杂度差异显著，且 go skill 无法在里程碑设计和阶段拆分之间插入审查环节。

**建议**：拆为三个动作——

| 动作 | 职责 | 产出 |
|------|------|------|
| `plan` | 读取需求/架构 → 审查 → 划分里程碑 | `design.md` |
| `design-milestone` | 读取 design.md → 编写里程碑设计 | `milestone-{N}/design.md` |
| `breakdown-stage` | 读取里程碑设计 → 编写 stage 文档 | `stage-{N}-{M}.md` |

前置依赖声明：`design-milestone` 依赖 `plan` 完成；`breakdown-stage` 依赖 `design-milestone` 完成。

### 1.2 [P0] 缺少 Worked Example

**现状**：三个输出模板（design.md、milestone design、stage）都是空壳占位符。

**问题**：LLM 无法从空模板推断内容的粒度和深度。参考 planner.md 提供了完整的 Stripe 订阅功能计划作为示例，architect.md 提供了项目特定的架构示例。

**建议**：添加一个完整但小规模的里程碑设计（含一个 stage 文档），用具体代码填充模板占位符。这是提升输出质量最有效的单一改进。

### 1.3 [P1] 输入输出缺少集中声明 + 参数约定

**现状**：`{seq}`、`{name}` 含义未说明；输出文件散落各段落中。

**问题**：go skill 需要从全文推断输出清单；路径模板含义模糊。

**建议**：

(a) 文档顶部增加参数说明：

```markdown
## 参数约定
- `{seq}`: 需求序号，由 go skill 传入（如 `001`）
- `{req-name}`: 需求名称，由 go skill 传入（如 `user-auth`）
- `{N}`: 里程碑序号（从 1 开始）
- `{M}`: 阶段序号（在里程碑内从 1 开始）
```

(b) 每个动作开头增加结构化的输入/输出表：

```markdown
### 输入
| 文档 | 路径 | 说明 |
|------|------|------|
| 需求文档 | `.vibewire/{seq}-{req-name}/requirements.md` | 由上游产出 |
| 架构文档 | `.vibewire/{seq}-{req-name}/architecture.md` | 由上游产出 |

### 输出
| 文档 | 路径 | 条件 |
|------|------|------|
| 总体设计 | `.vibewire/{seq}-{req-name}/design.md` | 正常情况 |
| 审查问题 | `.vibewire/{seq}-{req-name}/stager-issues.md` | 架构审查发现问题 |
```

### 1.4 [P1] 原则与步骤混杂

**现状**：`milestone` 动作中，"阶段划分原则"（规则）与"编写里程碑设计文档"（步骤）平铺在同一层级。

**问题**：执行者难以区分"必须严格遵守的规则"和"需要按序执行的操作"。参考 planner.md 将 Planning Process（步骤）和 Best Practices（原则）明确分开。

**建议**：每个动作内部分为三区域——

```markdown
## `plan` — 总体规划

### 规则
[此动作必须遵守的约束]

### 步骤
1. 读取文档 → 2. 架构审查 → 3. 划分里程碑 → 4. 输出 design.md

### 停止条件
- architecture.md 存在不合理之处 → 输出 stager-issues.md，暂停
```

将编写规范拆分：通用规范留在文档底部，stage 特有约束移入 `breakdown-stage` 动作内部。

### 1.5 [P2] design.md 模板缺少关键决策信息

**现状**：模板只有"概述 + 技术栈 + 里程碑列表 + 依赖关系"。

**问题**：缺少风险评估、跨里程碑共享基础设施、整体测试策略。

**建议**：增加——

```markdown
## 风险评估
| 里程碑 | 风险等级 | 主要风险 | 缓解措施 |
|--------|---------|---------|---------|

## 共享基础设施
[Milestone 1 中需提前建立、后续里程碑复用的模块]

## 整体测试策略
- 单元测试：[覆盖范围]
- 集成测试：[覆盖范围]
```

### 1.6 [P2] milestone design 模板中"行号范围"不合理

**现状**：模板要求"修改文件：列出需要修改的文件及行号范围"。

**问题**：设计阶段 stager 还没看具体代码，无法给出有意义的行号，要么猜测（降低可信度）要么花大量 token 阅读代码。

**建议**：改为"修改原因"，行号精确到 stage 文档的 Task 级别再给出。

```markdown
## 架构影响
- 涉及模块：[列出受影响的模块]
- 新增文件：[列出需要创建的文件]
- 修改文件：[列出需要修改的文件及修改原因]
```

---

## 二、TDD 流程与任务粒度（评估方向 2）

### 2.1 [P0] 缺少 Green 失败应对策略

**现状**：TDD 循环只定义了 Red→Green 的正常路径。

**问题**：Green 阶段测试仍不通过时没有处理策略，执行者可能陷入无限调试或直接放弃。参考 loop-operator.md 有"检测停滞并重试"机制。

**建议**：定义明确的失败处理路径——

1. 第一次失败 → 继续调试（限定重试次数）
2. 连续失败 → 暂停并考虑拆分当前 Stage
3. 仍无法通过 → 输出问题描述，交由 go skill 决定是否回退

### 2.2 [P0] 缺少 Task-Test 映射

**现状**：测试和 Task 之间没有关联标注，模板只有 `全部失败（Red）` 和 `全部通过（Green）` 两个状态。

**问题**：执行者无法判断完成 Task 1 后哪些测试应该通过，丧失增量反馈能力。

**建议**：

(a) 增加映射表——

```markdown
### 测试-任务映射

| 测试用例 | 关联 Task | 预期状态（Red 后） |
|----------|----------|-------------------|
| test_create_user | Task 1 | FAIL |
| test_validate_input | Task 1 | FAIL |
| test_user_persistence | Task 2 | FAIL |
| test_error_handling | Task 3 | FAIL |
```

(b) 在 Task 完成后增加进度检查步骤——

```markdown
**Step N：进度检查**
运行：`pytest tests/path/test.py -v -k "test_create_user or test_validate_input"`
预期：Task 1 关联测试通过
```

### 2.3 [P1] TDD 循环缺少 Refactor 阶段

**现状**：定义的循环是 Red-Green-Commit，没有 Refactor。

**问题**：参考 tdd-guide.md 的标准 TDD 循环是 Red-Green-**Refactor**，缺少重构窗口会导致技术债务跨 Stage 积累。

**建议**：在 Green 确认和 Commit 之间增加显式 Refactor 检查点——

```markdown
## 重构（Refactor）

审查刚写的代码是否有重复、命名不当、结构可优化的地方，修改后运行测试确认未破坏功能。

运行：`pytest tests/path/test.py -v`
预期：全部通过
```

### 2.4 [P1] 任务粒度指导不足

**现状**：只有"宁细勿粗"一条指导。

**问题**：缺少反向约束，容易导致过度拆分（每个 Task 只有几行代码变更，执行者频繁切换上下文）。

**建议**：增加双向粒度指导——

| 场景 | 建议 |
|------|------|
| 应拆分 | Task 涉及 3+ 文件修改；实现代码超过 50 行；Task 内有条件分支 |
| 应合并 | 两个 Task 修改同一文件相邻区域且合在一起更易保证一致性；Task 只有 1-2 行变更且无独立验证价值 |

给出数量建议：每个 Stage 含 2-5 个 Task 为宜，超过 5 个考虑拆分 Stage。

### 2.5 [P1] 测试策略层次不完整

**现状**：Stage 文档模板只有"单元测试"一个分类。

**问题**：缺少集成测试、错误路径测试、边界条件测试的规划位置。参考 tdd-guide.md 有 Unit / Integration / E2E 三层体系。

**建议**：将测试部分扩展为结构化层次——

```markdown
## 测试策略

### 功能测试（Red-Green 循环）
测试文件：`tests/path/to/test.py`

#### 单元测试
[函数级行为验证]

#### 集成测试（如涉及多模块交互）
[模块协作行为验证]

### 非功能性验证（各 Task 内）
[文件结构、配置正确性等即时检查]
```

增加要求：每个 Stage 的测试必须包含至少一个正常路径和一个异常路径。

### 2.6 [P2] 测试修改规则缺失

**现状**：没有定义"实现过程中发现测试需要调整"时的处理方式。

**问题**：一次写完一个 Stage 的所有测试，要求规划者精确预知所有接口设计，这在复杂 Stage 中几乎不可能。执行者在"遵循计划"和"响应实际情况"之间两难。

**建议**：明确说明——Red 阶段写出的测试是"初始设计"，实现过程中如果发现接口设计需要调整，允许修改测试，但必须注释说明修改原因。

### 2.7 [P2] Task 级别中间提交选项

**现状**：仅在 Stage Green 后才提交。

**问题**：当 Stage 包含多个 Task 且实现困难时，中间成果没有版本控制保护。

**建议**：当 Stage 超过 3 个 Task 时，允许在关键 Task 完成后做中间提交（`wip` 标记），Stage 最终提交仍为质量门控点。

---

## 三、Agent 最佳实践（评估方向 3）

### 3.1 [P1] 角色定义过于单薄

**现状**：只有"你是一个里程碑规划专家"一句话。

**问题**：缺少 expertise boundary（专业边界）声明。参考 security-reviewer 定义了 6 项 Core Responsibilities，refactor-cleaner 定义了 4 项。

**建议**：

(a) 增加 Core Responsibilities 节（4-6 项）：架构可行性审查、里程碑正交性设计、TDD 循环规划、任务粒度控制等。

(b) 增加"不做什么"节：不写实现代码、不运行测试、不执行 git 操作。

(c) 合并注意事项中分散的人设假设为统一 persona 描述。

### 3.2 [P1] 缺少停止规则

**现状**：只有"架构审查发现问题"一个错误路径。

**问题**：implementer.md 和 tester.md 都有明确的停止规则节，stager 缺少统一的异常处理模式。

**建议**：增加 Stop Rules 节——

- 需求文档不完整 → 暂停，报告缺失项
- architecture.md 存在矛盾 → 输出 stager-issues.md，暂停
- 技术栈无法确定 → 暂停，请求确认
- 里程碑出现循环依赖 → 重新规划
- 项目代码结构与 architecture.md 描述严重不符 → 暂停，请求确认

### 3.3 [P2] stager-issues.md 格式未定义

**现状**：提到输出 stager-issues.md 但没定义格式。

**建议**：定义结构化模板——

```markdown
# Stager 审查问题

## 问题 1
- **来源**：[哪个文档/哪段内容]
- **严重级别**：阻断 / 建议
- **描述**：[具体问题]
- **建议处理**：[修改方向或回退到哪个阶段]
```

### 3.4 [P2] 与下游 agent 的协作接口未声明

**现状**：stager 输出被 tester/implementer/reviewer 消费，但未定义"输出契约"。

**问题**：如果 stager 的 Task 编号格式、测试文件路径格式与下游 agent 的解析规则不一致，下游会解析失败。

**建议**：增加"输出契约"节——

```markdown
## 输出契约
- stage 文档中 Task 编号格式为 `Task {数字}`，tester 和 implementer 依赖此编号调度
- 测试文件路径必须为绝对路径或相对于项目根的路径
- 每个 Task 的"文件"节必须列出所有涉及的文件路径
```

### 3.5 [P2] "项目代码结构"读取范围模糊

**现状**：笼统一提"项目代码结构"。

**问题**：缺少分层读取策略，大项目可能导致上下文溢出。参考 code-reviewer 有精确的上下文获取步骤。

**建议**：细化为——

1. `Glob` 获取目录树结构
2. 读取关键配置文件（package.json / pyproject.toml 等）确认技术栈
3. 按需读取 architecture.md 中涉及的模块入口文件，不读全部

增加"按需读取"原则：只在架构审查发现疑问时才深入读取相关代码。

### 3.6 [P2] stage 模板中代码示例绑定 Python

**现状**：模板中 `pytest`、`def test_xxx()` 为 Python 风格。

**建议**：标注为"示例，按项目实际技术栈调整"，或改为技术栈无关的伪代码。

### 3.7 [P2] 缺少输出自检清单

**现状**：输出文档后没有自检环节。

**建议**：借鉴 refactor-cleaner 的 Safety Checklist，增加自检项——

```markdown
## 输出自检
- [ ] 每个里程碑可独立合并和验证
- [ ] 每个 Task 给出了完整文件路径
- [ ] TDD 循环完整（Red 有预期失败命令，Green 有预期通过命令）
- [ ] 无 Task 依赖后续阶段才创建的代码
- [ ] 里程碑完成标准可映射到具体测试
```

### 3.8 [P3] 执行者人设假设存在矛盾

**现状**：第 197 行说"假设执行工程师对我们的代码库毫无了解"，第 198 行说"假设执行者是有经验的开发者"。

**建议**：统一为——"假设执行者是资深开发者，但不熟悉本项目的技术栈、代码库和问题领域，且对测试设计缺乏品味。因此每个任务必须包含完整的文件路径、完整代码和明确的验证步骤。"

---

## 参考文件中的优秀模式汇总

| 模式 | 来源 | stager 可借鉴点 |
|------|------|-----------------|
| Worked Example | planner.md, architect.md | 用具体案例填充模板，引导输出粒度 |
| 分层检查清单 + 严重级别 | code-reviewer.md | 架构审查增加分级清单（CRITICAL/HIGH/MEDIUM） |
| Step-by-step 工作流 + Safety Checklist | refactor-cleaner.md | 内部步骤显式编号，每步有输出物和校验点 |
| 结构化检查表 | security-reviewer.md | 架构审查增加检查表（技术可行性、模块正交性、依赖完整性） |
| 核心职责 + 不做什么 | refactor-cleaner.md, security-reviewer.md | 增加 Core Responsibilities 节 |
| 明确的停止规则 | implementer.md, tester.md | 增加 Stop Rules 节 |
| 结构化 issues 输出 | reviewer.md | 为 stager-issues.md 定义格式模板 |
| Red-Green-Refactor 完整循环 | tdd-guide.md | 增加 Refactor 阶段 |
