---
name: intro
description: Use ONLY when the user explicitly invokes /vibewire:intro. Do not auto-trigger based on codebase analysis or documentation staleness assumptions.
---

# Intro

扫描项目代码库，创建 `.vibewire/project.md` 和 `.vibewire/CHANGELOG.md`，建立文档基线。

## Process

### 1. Confirm Scope

- 检查项目是否为 git 仓库，若不是则执行 `git init` 初始化
- 确认 `.vibewire/` 未被 `.gitignore` 排除
- 若 `.vibewire/` 不存在，直接进入 Explore
- 若已存在，提示用户确认是否覆盖重建（旧文件将被删除后全量重建）

### 2. Explore

从项目根目录结构扫描开始，识别目录划分和特征文件。

**扫描范围控制：**

- 排除目录：`node_modules`, `vendor`, `.git`, `__pycache__`, `.next`, `dist`, `build`, `target`, `.venv`, `venv`, `env`
- 排除文件：锁文件（`package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `Cargo.lock`, `poetry.lock`）、二进制文件
- 深度策略：优先扫描前 3 层目录结构，对深层模块按需深入

**扫描维度：**

- **项目概述** — 优先从 README、CLAUDE.md、package.json description 等已有文档提取
- **技术栈** — 通过特征文件识别（package.json, Cargo.toml, go.mod, pyproject.toml, pom.xml 等）
- **模块架构** — 通过目录划分和 import/require 依赖关系识别模块职责
- **编码约定** — 命名风格、错误处理模式、测试组织、lint 配置

### 3. Write Docs

创建 `.vibewire/` 目录，按以下格式写入文件。

**project.md** 结构：

```markdown
> Last updated: yyyy-mm-dd | Intro

# 项目概述
{基于探索结果的项目描述，2-5 句话}

# 目录结构
{代码目录树，包含源码、测试、配置和依赖文件。排除文档、IDE 配置、CI 脚本等非代码文件}

# 当前架构
{模块职责、模块间依赖关系、核心数据流}

# 技术栈
{语言、框架、数据库、关键依赖及版本}

# 约定与规范
{编码规范、目录约定、公共模式。若无显著特征可省略此节}
```

**CHANGELOG.md** 结构：

```markdown
# 变更记录

## yyyy-mm-dd | Intro
- {项目基线摘要，列出主要模块和技术栈}
```

### 4. Verify

对 project.md 中引用的内容进行结构化验证：

1. 提取文档中出现的所有文件路径和目录路径
2. 逐个验证路径是否存在
3. 抽查技术栈版本号与实际依赖文件是否一致
4. 若发现不存在的路径或版本偏差，回退到 Explore 修正对应部分

### 5. Commit

执行以下命令提交变更：

```bash
git add .vibewire/project.md .vibewire/CHANGELOG.md
git commit -m "[vibewire/intro] docs: init project documentation"
```

## Key Principles

- **可验证** — 只记录可从代码库中验证的观察结果，不做推测或规划
- **精确路径** — 文档中引用的文件路径必须精确完整，不含猜测路径
- **不遗漏** — 扫描范围内所有文件和模块都必须反映在文档中
- **排除噪音** — 依赖包、构建产物、锁文件不纳入文档范围
