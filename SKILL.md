---
name: agile-dev
version: 2.0.0
description: "轻量级企业迭代开发方法论 — 双Agent协作模型（规划Agent + 交付Agent），通过 plan/to-do/ 队列异步协作。当用户需要进行功能迭代、需求变更、代码修改时使用。"
---

# 轻量级企业迭代开发方法论（Lean Enterprise Iteration）

适用于个人/小团队产品开发的工程方法论。继承 Agile 精神，融合企业级质量门禁与可追溯性，同时保持轻量——每个仪式都有实际价值，不为流程而流程。

当用户表达以下意图时启用本 Skill：增加功能、增加业务场景、收到新需求、扩展能力、修改现有行为、调整数据结构等。

## 项目上下文

项目完整信息见项目根目录 `CLAUDE.md`。开发前先读取该文件了解最新项目状态。

## 双Agent协作模型

本方法论将迭代开发拆分为两个可并行运行的 Agent：

| Agent | 覆盖阶段 | 产出 | 启动方式 |
|-------|---------|------|---------|
| **规划Agent** | 阶段 1-3（需求分析→方案设计→版本计划） | Plan 文件写入 `plan/to-do/` | 加载 `agile-dev` Skill，推进阶段 1-3 |
| **交付Agent** | 阶段 4-5（实施验证→记录发布） | 代码变更、Git commit | 加载 `agile-dev` Skill，从 `plan/to-do/` 拉取版本 |

两者通过 `plan/to-do/` 队列解耦：规划Agent 写，交付Agent 消费。可并行运行（规划Agent 准备 v2 的同时交付Agent 实施 v1）。

## 详细流程文件

完整的阶段定义、门禁清单、角色职责等见本 Skill 目录下的以下文件：

| 文件 | 覆盖阶段 | 归属 Agent |
|------|---------|-----------|
| `methodology.md` | 全流程总纲（核心哲学、目录结构、编码规范、测试策略、风险管理、DoD 等） | 两者共享 |
| `需求分析与方案设计.md` | 阶段 1-2（需求澄清→方案设计） | 规划Agent |
| `制订方案实施版本计划.md` | 阶段 3（Goal→Epic→Story 拆分 + 版本划分） | 规划Agent |
| `版本实施与发布.md` | 阶段 4-5（实施验证→记录发布） | 交付Agent |

## 项目适配指南

新项目采用本方法论时，需要完成以下准备工作：

### 1. 目录初始化

在项目根目录执行：

```bash
mkdir -p define/adr data/.backup requirements change-log versions plan/to-do plan/done testing/test-report sandbox
```

如项目使用不同目录结构（如 `src/` 替代 `scripts/`），可在 `CLAUDE.md` 中声明实际结构。

### 2. CLAUDE.md 配置

项目根目录的 `CLAUDE.md` 应至少包含以下内容，确保两个 Agent 能独立获取完整上下文：

- **项目路径** — 项目根目录的绝对路径
- **目录结构** — 当前项目的目录树概览
- **技术栈** — 编程语言、运行环境、依赖约束
- **数据模型** — 核心实体、字段、关联关系
- **关键规则** — 业务规则、设计约束、禁止事项
- **命令/API 参考** — 可执行命令或 API 端点列表
- **编码规范** — 命名规范、输出格式、错误处理约定

### 3. 初始文件创建

- `requirements/requirements.md` — 创建空白需求文档（含修订记录表）
- `requirements/scenario.md` — 创建空白场景文档
- `testing/test-case.md` — 创建空白测试用例文档
- `versions/roadmap.md` — 创建空白版本路线图
- `data/enum-dictionary.json` — 创建枚举字典（如项目有枚举值）

### 4. 启动迭代

1. **规划Agent 会话**：向 Claude Code 描述需求变更，Skill 自动激活，从阶段 1 推进到阶段 3
2. **交付Agent 会话**：指定要交付的版本号，从 `plan/to-do/` 拉取并实施

两个会话可在独立终端并行运行，互不阻塞。
