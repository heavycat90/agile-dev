---
name: agile-dev
version: 2.2.0
description: "轻量级企业迭代开发方法论 — 五Agent协作（需求分析Agent、规划Agent、交付Agent、QA Agent、PMO Agent），双轨工作模型（快速通道 + 标准通道 + 需求池），通过 plan/to-do/ 和 plan/verify/ 队列异步协作。当用户需要进行功能迭代、需求变更、代码修改、查看项目状态时使用。"
---

当用户表达以下意图时启用本 Skill：增加功能、增加业务场景、收到新需求、扩展能力、修改现有行为、调整数据结构、积累需求、查看项目状态、更新看板等。

## 双轨工作模型

本方法论提供两条实施轨道 + 一个需求缓冲区：

| 轨道 | 适用场景 | 流程 |
|------|---------|------|
| 🟢 **快速通道** | 小增补，立即要（≤ 2 文件、无迁移、无新实体） | 分析 → 设计 → 分流 → 直接实施 |
| 🔵 **标准通道** | 中大型变更，需规划 | 分析 → 设计 → 分流 → 版本计划 → 实施 |
| 📥 **需求池** | 暂不实施，积累打磨 | 分析 → 设计 → 分流 → 存入 backlog，后续选取 |

## 五Agent协作模型

| Agent | 覆盖阶段 | 产出 |
|-------|---------|------|
| **需求分析Agent** | 阶段 1-2（需求分析→方案设计→分流决策） | `requirements/`、backlog 条目 |
| **规划Agent** | 阶段 3（标准通道：从需求池选取 + Goal→Epic→Story 拆分 + 版本划分） | Plan 文件写入 `plan/to-do/`（标准通道） |
| **交付Agent** | 阶段 4（快速通道或标准通道：实施与移交）+ 阶段 6（记录与发布） | 代码变更、Git commit |
| **QA Agent** | 阶段 5（独立验证 + 环境管理 + 质量门禁判定） | `qa/reports/`、`qa/metrics.md`、`testing/test-case.md`、Plan 文件移入 `plan/done/` |
| **PMO Agent** | 项目监控（扫描数据源，生成看板） | `pmo/dashboard.md` |

标准通道：需求分析Agent → 规划Agent（`requirements/` 文档交接）→ 交付Agent（`plan/to-do/` 队列）→ QA Agent（`plan/verify/` 队列）。快速通道：需求分析Agent 直接将方案要点传递给交付Agent，跳过规划Agent。PMO Agent 在下游消费前四者产出，独立运行。

## 流程文件

| 文件 | 覆盖 | 归属 |
|------|------|------|
| `references/methodology.md` | 全流程总纲（五Agent模型、双轨模型、需求池、生命周期、目录结构、角色定义、可追溯矩阵、门禁矩阵、多环境与 Git 分支） | 五者共享 |
| `references/design-principles.md` | 核心哲学、编码规范、设计原则、测试策略、沙箱隔离 | 五者共享 |
| `references/dod-and-qa.md` | 定义完成（DoD）、质量度量指标 | 交付Agent、QA Agent、PMO Agent |
| `references/risk-and-exceptions.md` | 风险管理、ADR、Hotfix、回滚策略、禁止事项 | 五者共享 |
| `references/analyst/requirements-analysis.md` | 阶段 1-2（需求澄清→方案设计→分流决策：快速/标准/backlog） | 需求分析Agent |
| `references/planner/version-planning.md` | 阶段 3（标准通道：从需求池选取 + Goal→Epic→Story 拆分 + 版本划分） | 规划Agent |
| `references/deliverer/implementation.md` | 阶段 4 + 阶段 6（快速通道 + 标准通道：实施与移交→记录与发布） | 交付Agent |
| `references/qa/quality-assurance.md` | 阶段 5（独立验证：环境准备→定向验证→冒烟测试→安全/压力测试→质量度量） | QA Agent |
| `references/pmo/reporting.md` | 扫描项目数据源，生成/更新 `pmo/dashboard.md` | PMO Agent |

## 新项目适配

### 1. 读取 CLAUDE.md

开发前先读取项目根目录 `CLAUDE.md` 了解项目状态（配置要求详见 `references/methodology.md`）。

### 2. 初始化目录

```bash
mkdir -p define/adr data/.backup requirements change-log versions plan/to-do plan/verify plan/done pmo testing/test-report qa/reports envs/qa/data envs/qa/config envs/qa/sandbox envs/stg/data envs/stg/config envs/stg/sandbox envs/prod/data envs/prod/config envs/prod/sandbox envs/pen-test/data envs/pen-test/config envs/pen-test/sandbox envs/stress-test/data envs/stress-test/config envs/stress-test/sandbox sandbox
```

### 3. 创建初始文件

- `requirements/requirements.md`、`requirements/scenario.md`、`requirements/backlog.md`
- `testing/test-case.md`
- `versions/roadmap.md`
- `pmo/dashboard.md`（空白模板）
- `qa/metrics.md`（空白模板）
- `data/enum-dictionary.json`（如项目有枚举值）

### 4. 启动迭代

1. **需求分析Agent 会话**：描述需求变更，Skill 自动激活，从阶段 1 推进到分流决策
2. **规划Agent 会话**：标准通道下，从需求分析Agent 接手，进入阶段 3，产出 Plan 文件到 `plan/to-do/`
3. **交付Agent 会话**：快速通道 — 需求分析Agent 传递方案要点直接实施；标准通道 — 从 `plan/to-do/` 拉取版本实施，完成后移交 `plan/verify/`
4. **QA Agent 会话**：从 `plan/verify/` 拉取验证任务，执行测试、判定门禁 4，将 Plan 移入 `plan/done/`
5. **PMO Agent 会话**：询问项目状态，Skill 自动激活，扫描数据源生成看板

各 Agent 可在独立终端并行运行，互不阻塞。
