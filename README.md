# Vibewire

Agent自主开发流程插件 - 完全自动化从需求到代码的端到端开发。

## 概述

Vibewire 是一个 Claude Code 插件，实现 Agent 完全自主的端到端开发流程。用户只需输入任务描述，Agent 会自动完成需求分析、架构设计、代码实现、测试验证等全部环节。

## 核心特性

- **完全自主**: 用户只需输入任务描述，全程无介入，最后验收结果
- **两层迭代**: 阶段迭代 + 任务批次迭代
- **过程可追溯**: 所有过程资料保存在 `.vibewire/` 目录

## 使用方式

```bash
# 在任意项目中使用
/go 实现一个用户登录功能，支持邮箱密码和OAuth登录
```

## 目录结构

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

## 执行流程

1. **前期准备**: 需求分析 → 架构设计
2. **阶段规划**: 划分执行阶段
3. **阶段执行**: 每个阶段内进行批次迭代（实现 → 测试 → 审查）
4. **完成**: 生成最终总结

## Agent 职责

| Agent | 职责 |
|-------|------|
| requirement-analyzer | 分析用户任务，提取需求点 |
| architect | 设计技术方案和架构 |
| planner | 划分执行阶段和任务批次 |
| implementer | 按批次执行代码实现 |
| tester | 编写/执行测试 |
| reviewer | 审查代码质量 |

## 版本

- v0.1.0: 最小可运行版本

## License

MIT
