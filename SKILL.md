---
name: agile-dev
version: 2.0.0
description: "轻量级企业迭代开发方法论 — 双Agent协作模型（规划Agent + 交付Agent），通过 plan/to-do/ 队列异步协作。当用户需要进行功能迭代、需求变更、代码修改时使用。"
---

当用户表达以下意图时启用本 Skill：增加功能、增加业务场景、收到新需求、扩展能力、修改现有行为、调整数据结构等。

## 双Agent协作模型

本方法论将迭代开发拆分为两个可并行运行的 Agent：

| Agent | 覆盖阶段 | 产出 |
|-------|---------|------|
| **规划Agent** | 阶段 1-3（需求分析→方案设计→版本计划） | Plan 文件写入 `plan/to-do/` |
| **交付Agent** | 阶段 4-5（实施验证→记录发布） | 代码变更、Git commit |

两者通过 `plan/to-do/` 队列解耦：规划Agent 写，交付Agent 消费。可并行运行（规划Agent 准备 v2 的同时交付Agent 实施 v1）。

## 流程文件

完整的阶段定义、门禁清单、角色职责见以下文件。核心哲学、编码规范、DoD、风险管理等共享标准定义在 `methodology.md`——两个 Agent 共同遵循。

| 文件 | 覆盖阶段 | 归属 Agent |
|------|---------|-----------|
| `methodology.md` | 全流程总纲（核心哲学、目录结构、编码规范、测试策略、风险管理、DoD 等） | 两者共享 |
| `planner/analysis-and-design.md` | 阶段 1-2（需求澄清→方案设计） | 规划Agent |
| `planner/version-planning.md` | 阶段 3（Goal→Epic→Story 拆分 + 版本划分） | 规划Agent |
| `deliverer/implementation.md` | 阶段 4-5（实施验证→记录发布） | 交付Agent |

## 新项目适配

### 1. 读取 CLAUDE.md

开发前先读取项目根目录 `CLAUDE.md` 了解项目状态。该文件应包含项目路径、技术栈、数据模型、关键规则、命令/API 参考等，确保两个 Agent 能独立获取完整上下文（配置要求详见 `methodology.md`）。

### 2. 初始化目录

在项目根目录执行（完整目录结构见 `methodology.md`）：

```bash
mkdir -p define/adr data/.backup requirements change-log versions plan/to-do plan/done testing/test-report sandbox
```

### 3. 创建初始文件

- `requirements/requirements.md`、`requirements/scenario.md`
- `testing/test-case.md`
- `versions/roadmap.md`
- `data/enum-dictionary.json`（如项目有枚举值）

### 4. 启动迭代

1. **规划Agent 会话**：描述需求变更，Skill 自动激活，从阶段 1 推进到阶段 3
2. **交付Agent 会话**：指定交付版本号，从 `plan/to-do/` 拉取并实施

两个会话可在独立终端并行运行，互不阻塞。
