---
name: agile-dev
version: 2.1.0
description: "轻量级企业迭代开发方法论 — 双轨工作模型（快速通道 + 标准通道 + 需求池），双Agent协作（规划Agent + 交付Agent），通过 plan/to-do/ 队列异步协作。当用户需要进行功能迭代、需求变更、代码修改时使用。"
---

当用户表达以下意图时启用本 Skill：增加功能、增加业务场景、收到新需求、扩展能力、修改现有行为、调整数据结构、积累需求等。

## 双轨工作模型

本方法论提供两条实施轨道 + 一个需求缓冲区：

| 轨道 | 适用场景 | 流程 |
|------|---------|------|
| 🟢 **快速通道** | 小增补，立即要（≤ 2 文件、无迁移、无新实体） | 分析 → 设计 → 分流 → 直接实施 |
| 🔵 **标准通道** | 中大型变更，需规划 | 分析 → 设计 → 分流 → 版本计划 → 实施 |
| 📥 **需求池** | 暂不实施，积累打磨 | 分析 → 设计 → 分流 → 存入 backlog，后续选取 |

## 双Agent协作模型

| Agent | 覆盖阶段 | 产出 |
|-------|---------|------|
| **规划Agent** | 阶段 1-2（需求分析→方案设计→分流）+ 阶段 3（标准通道：版本计划） | Plan 文件写入 `plan/to-do/`（标准通道）；backlog 条目 |
| **交付Agent** | 阶段 4-5（快速通道或标准通道：实施验证→记录发布） | 代码变更、Git commit |

标准通道下两者通过 `plan/to-do/` 队列解耦，可并行运行（规划Agent 准备 v2 的同时交付Agent 实施 v1）。快速通道下规划Agent 完成后直接将方案要点传递给交付Agent。

## 流程文件

| 文件 | 覆盖 | 归属 |
|------|------|------|
| `methodology.md` | 全流程总纲（双轨模型、需求池、核心哲学、目录结构、编码规范、DoD、风险管理等） | 两者共享 |
| `planner/analysis-and-design.md` | 阶段 1-2（需求澄清→方案设计→分流决策：快速/标准/backlog） | 规划Agent |
| `planner/version-planning.md` | 阶段 3（标准通道：从需求池选取 + Goal→Epic→Story 拆分 + 版本划分） | 规划Agent |
| `deliverer/implementation.md` | 阶段 4-5（快速通道 + 标准通道：实施验证→记录发布） | 交付Agent |

## 新项目适配

### 1. 读取 CLAUDE.md

开发前先读取项目根目录 `CLAUDE.md` 了解项目状态（配置要求详见 `methodology.md`）。

### 2. 初始化目录

```bash
mkdir -p define/adr data/.backup requirements change-log versions plan/to-do plan/done testing/test-report sandbox
```

### 3. 创建初始文件

- `requirements/requirements.md`、`requirements/scenario.md`、`requirements/backlog.md`
- `testing/test-case.md`
- `versions/roadmap.md`
- `data/enum-dictionary.json`（如项目有枚举值）

### 4. 启动迭代

1. **规划Agent 会话**：描述需求变更，Skill 自动激活，从阶段 1 推进到分流决策
2. **交付Agent 会话**：快速通道 — 规划Agent 传递方案要点直接实施；标准通道 — 从 `plan/to-do/` 拉取版本实施

两个会话可在独立终端并行运行，互不阻塞。
