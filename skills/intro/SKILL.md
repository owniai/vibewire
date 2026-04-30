---
name: intro
description: Use ONLY when the user explicitly invokes /vibewire:intro. Do not auto-trigger based on codebase analysis or documentation staleness assumptions.
---

# Intro: Scan and Establish Documentation Baseline

扫描项目现状，建立文档基线，为后续工作流提供可引用的项目上下文。

## Process

### 1. Ensure Environment

**vibewire 自身版本检查：**
1. 执行 `claude plugins list --json | python -c "import sys,json;d=json.load(sys.stdin);p=next((x for x in d if x['id']=='vibewire@vibewire'),None);print(p['version'] if p else 'not installed')"` 获取当前版本
2. 询问用户是否检查更新，确认后执行 `claude plugins update vibewire@vibewire`
3. 若版本有变化：**停止后续流程**，提示用户新开会话重新执行 `/vibewire:intro`

**插件管理（仅在 vibewire 为最新版时继续）：**
3. 检查 peek-code 是否已安装
4. 若未安装：执行 `claude plugins install peek-code@vibewire` 安装
5. 若已安装：执行 `claude plugins update peek-code@vibewire` 更新到最新版

**CLI 确保：**
6. 执行 `peek --version`，若未找到则按以下顺序尝试安装（跳过当前平台不可用的工具）：
   - Homebrew (macOS/Linux): `brew install owniai/tap/peek`
   - cargo-binstall: `cargo binstall peek -y`
   - cargo install: `cargo install peek --locked`
   - cargo install from git: `cargo install --git https://github.com/owniai/peek --locked`
7. 若已安装，检测所用包管理器并执行对应更新命令：
   - Homebrew: `brew upgrade peek`
   - cargo-binstall / cargo: `cargo install peek --locked`
8. 再次执行 `peek --version` 确认最终版本

**加载 skill：**
9. 通过 Skill 工具加载 `peek-code:peek-code` skill，为后续步骤提供 `peek` 命令辅助探索代码库

**故障排查：**
- 安装后 `peek` 仍找不到：确认 `~/.cargo/bin`（Windows 为 `%USERPROFILE%\.cargo\bin`）在 `$PATH` 中
- Rust 未安装：询问用户是否安装 Rust，确认后协助安装再重试

### 2. Confirm Scope

1. 检查 `.vibewire/project.md` 和 `.vibewire/CHANGELOG.md` 是否存在
   - 若均存在：提示用户确认是否覆盖重建（旧文件将被删除后全量重建）
   - 若不存在或用户确认重建：继续下一步
2. 检查项目是否为 git 仓库，若不是则执行 `git init` 初始化
3. 确认 `.vibewire/` 未被 `.gitignore` 排除

### 3. Explore

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

### 4. Write Docs

创建 `.vibewire/` 目录，按以下格式写入文件。

**.vibewire/project.md** 结构：

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

**.vibewire/CHANGELOG.md** 结构：

```markdown
# 变更记录

## yyyy-mm-dd | Intro
- {项目基线摘要，列出主要模块和技术栈}
```

### 5. Self-Review

检查本次执行是否符合规范：
1. 文档结构是否符合 Write Docs 中定义的格式要求
2. 内容是否遵守 Key Principles（可验证、精确路径、不遗漏、排除噪音）
3. 若发现问题，修正对应部分

### 6. Commit

一次性提交所有变更：

```bash
git add .vibewire/project.md .vibewire/CHANGELOG.md
git commit -m "[vibewire/intro] docs: init project documentation baseline"
```

## Key Principles

- **可验证** — 只记录可从代码库中验证的观察结果，不做推测或规划
- **精确路径** — 文档中引用的文件路径必须精确完整，不含猜测路径
- **不遗漏** — 扫描范围内所有文件和模块都必须反映在文档中
- **排除噪音** — 依赖包、构建产物、锁文件不纳入文档范围
- **忽略格式检查** — markdown lint 等文档格式告警一律忽略，内部规划文档不适用项目文档格式规范

## Anti-Pattern

- **推测性记录** — 未在代码库中观察到的事实不应出现在文档中
- **遗漏后继续** — 发现未覆盖的模块时不得跳过，必须补充探索
- **过度深入** — 不对单个模块做逐文件分析，保持架构层面的概览
