# 轻量级企业迭代开发方法论（Lean Enterprise Iteration）

> 适用于个人/小团队产品开发的工程方法论。继承 Agile 精神，融合企业级质量门禁与可追溯性，同时保持轻量——每个仪式都有实际价值，不为流程而流程。

当用户表达以下意图时启用本 Skill：增加功能、增加业务场景、收到新需求、扩展能力、修改现有行为、调整数据结构、新增命令等。

---

## 核心哲学与设计原则

五条核心价值观与编码规范、测试策略定义在 [`design-principles.md`](design-principles.md)。所有 Agent 实施时以此为唯一标准。

---

## 项目上下文

项目完整信息见项目根目录 `CLAUDE.md`。开发前先读取该文件了解最新项目状态，包括但不限于：

- 项目路径与技术栈
- 数据模型与字段定义
- CLI 命令参考或 API 文档
- 编码规范与设计约束

**适配要求**：采用本方法论的项目，`CLAUDE.md` 应至少包含以下内容，确保各 Agent 能独立获取上下文：

1. **项目路径** — 项目根目录的绝对路径
2. **目录结构** — 当前项目的目录树概览（可简化）
3. **技术栈** — 编程语言、运行环境、依赖约束
4. **数据模型** — 核心实体、字段、关联关系
5. **关键规则** — 业务规则、设计约束、禁止事项
6. **命令/API 参考** — 可执行命令或 API 端点列表

---

## 标准目录结构

本方法论推荐以下目录结构。首次使用前，确保所有目录均已创建：

```
<project>/                           # 项目根目录
│
├── define/                         # 领域定义（数据结构、枚举、ADR）
│   ├── <entity>.md                 #   各实体字段定义
│   ├── enum-dictionary.md          #   枚举值字典
│   └── adr/                        #   架构决策记录（ADR）
│
├── data/                           # 实际数据（json / yaml / 等）
│   ├── <entity>.json               #   业务数据文件
│   ├── enum-dictionary.json        #   枚举值（预填）
│   └── .backup/                    #   数据备份（回滚用，实施前创建）
│
├── src/ 或 scripts/                # 源代码
│   └── <entry-point>               #   项目入口（CLI / API / 等）
│
├── requirements/                   # 需求文档
│   ├── requirements.md             #   需求定稿（含修订记录）
│   ├── scenario.md                 #   业务场景 + 运维场景
│   └── backlog.md                  #   需求池（未排期的需求条目）
│
├── change-log/                     # 变更记录（每版本一个文件）
│
├── versions/                       # 产品版本路线图与发布说明
│   ├── roadmap.md                  #   版本路线图（所有版本概览）
│   └── v{x.y.z}.md                 #   各版本发布说明
│
├── plan/                           # 版本实施计划
│   ├── to-do/                      #   待实施的 Story Plan（规划→交付 队列）
│   │   └── v{N}/                   #     按版本组织（v1, v2, ...）
│   ├── verify/                     #   待验证的 Story Plan（交付→QA 队列）
│   │   └── v{N}/
│   └── done/                       #   已验证完成的 Story Plan（QA 产出）
│       └── v{N}/
│
├── pmo/                            # PMO 项目看板
│   └── dashboard.md                #   项目状态看板（PMO Agent 维护）
│
├── qa/                             # QA 质量保证
│   ├── reports/                    #   测试报告（每版本）
│   │   └── v{x.y.z}-report.md
│   └── metrics.md                  #   质量指标汇总
│
├── testing/                        # 测试
│   ├── test-case.md                #   测试用例定义
│   ├── test-runner.<ext>           #   自动化测试脚本
│   └── test-report/                #   测试报告
│
├── envs/                           # 多环境配置与数据
│   ├── qa/                         #   功能测试环境（QA Agent 主战场）
│   │   ├── data/                   #     测试数据
│   │   ├── config/                 #     QA 配置
│   │   └── sandbox/                #     隔离沙箱
│   ├── stg/                        #   预发布验证环境
│   │   ├── data/                   #     脱敏生产级数据
│   │   ├── config/                 #     类生产配置
│   │   └── sandbox/
│   ├── prod/                       #   生产环境
│   │   ├── data/                   #     真实数据（交付Agent 管理）
│   │   ├── config/                 #     生产配置
│   │   └── sandbox/                #     只读沙箱
│   ├── pen-test/                   #   安全测试环境
│   │   ├── data/                   #     脱敏测试数据
│   │   ├── config/                 #     安全测试配置
│   │   └── sandbox/
│   └── stress-test/                #   负载/压力测试环境
│       ├── data/                   #     生成的负载数据
│       ├── config/                 #     压力测试配置
│       └── sandbox/
│
├── sandbox/                        # 开发沙箱（临时，每次测试前重建）
│
├── .claude/                        # Claude Code 配置
│   └── settings.json               #   项目级权限与配置
│
└── CLAUDE.md                       # 项目上下文（数据模型、命令参考等）
```

> 注：上述目录名和文件名均为推荐约定。项目可根据实际情况调整，但应在 `CLAUDE.md` 中明确实际使用的目录结构。
>
> `envs/` 下的 5 个环境中，`qa`、`stg`、`prod` 是核心环境（功能验证 → 预发布 → 生产）；`pen-test` 和 `stress-test` 是按需环境（安全测试和压力测试由 QA Agent 在需要时激活）。

### 目录初始化（自动执行）

**本 Skill 被激活后，在进入任何阶段之前，必须先执行以下目录初始化命令。** 缺失目录会阻断后续流程（如阶段 3 无法输出 Plan 文件、阶段 4 无法备份数据）。

```bash
mkdir -p define/adr data/.backup requirements change-log versions plan/to-do plan/verify plan/done pmo qa/reports testing/test-report sandbox \
  envs/qa/{data,config,sandbox} envs/stg/{data,config,sandbox} envs/prod/{data,config,sandbox} \
  envs/pen-test/{data,config,sandbox} envs/stress-test/{data,config,sandbox}
```

执行规则：
- 上述命令在项目根目录执行，幂等，可重复执行
- 必须在读取 `CLAUDE.md` 之后、进入阶段 1 之前完成
- 若任一目录创建失败（如权限不足），中止流程并告知用户
- 如项目使用不同目录结构（如 `src/` 替代 `scripts/`），按 `CLAUDE.md` 中的实际结构调整

### 目录与阶段的映射关系

| 阶段 | Agent | 轨道 | 读取目录 | 写入/产出目录 |
|------|-------|------|---------|-------------|
| 阶段 1: 需求分析 | 需求分析 | 全部 | `requirements/`、`testing/` | `requirements/` |
| 阶段 2: 方案设计 | 需求分析 | 全部 | `define/`、`data/`、源码目录 | `define/`（含 `adr/`）、`data/enum-dictionary.json` |
| 阶段 3: 版本计划 | 规划 | 标准 | `requirements/`、`testing/`、`versions/`、`requirements/backlog.md` | `plan/to-do/`、`versions/roadmap.md`、`versions/v{x.y.z}.md` |
| 阶段 4: 实施 | 交付 | 快速 + 标准 | `plan/to-do/`（标准）、`define/`、源码目录、`data/` | 源码目录、`data/`、`data/.backup/`、`plan/verify/` |
| 阶段 5: 验证 | QA | 快速 + 标准 | `plan/verify/`、`testing/`、`envs/qa/` | `qa/reports/`、`qa/metrics.md`、`plan/done/`、`envs/*/sandbox/` |
| 阶段 6: 发布 | 交付 | 快速 + 标准 | `CLAUDE.md`、`change-log/`、`versions/`、`qa/reports/` | `CLAUDE.md`、`change-log/`、`versions/v{x.y.z}.md`（更新） |
| 环境管理 | QA | 全部 | `envs/`、`data/` | `envs/*/{data,config,sandbox}/` |
| 需求池写入 | 需求分析 | — | `requirements/backlog.md` | `requirements/backlog.md`（新增、精炼条目） |
| 需求池选取 | 规划 | 标准 | `requirements/backlog.md` | `requirements/backlog.md`（标记 done） |
| 看板更新 | PMO | 全部 | `requirements/backlog.md`、`plan/to-do/`、`plan/verify/`、`plan/done/`、`versions/`、`change-log/`、`qa/` | `pmo/dashboard.md` |

### 版本号管理规则

采用语义化版本号（Semantic Versioning）：`MAJOR.MINOR.PATCH`（如 `1.2.0`）

| 版本位 | 递增条件 | 示例 |
|--------|---------|------|
| **MAJOR** | 不兼容的破坏性变更（数据格式变化、命令重命名、字段删除） | `1.2.0` → `2.0.0` |
| **MINOR** | 向后兼容的新功能（新增字段、新增命令、新增实体） | `1.2.0` → `1.3.0` |
| **PATCH** | 向后兼容的缺陷修复（Bug fix、错误信息优化、性能改进） | `1.2.0` → `1.2.1` |

附加规则：
- 初始版本号为 `1.0.0`
- 一个 Release Version 对应一次版本号递增
- 若同一次迭代中拆分多个 Release Version，每个独立递增版本号
- MAJOR 递增时，MINOR 和 PATCH 归零；MINOR 递增时，PATCH 归零
- 版本号由阶段 3 分配，阶段 6 确认后正式生效

---

## 双轨工作模型

并非所有变更都需要完整的 6 阶段流程。本方法论提供两条实施轨道 + 一个需求缓冲区，以适应不同的业务节奏：

### 轨道总览

```
                      需求输入
                         │
                         ▼
              阶段 1-2: 需求分析 + 方案设计
                         │
                         │  分流决策（≤ 2 文件、无迁移、无新实体？）
                         │
            ┌────────────┼────────────┐
            ▼            ▼            ▼
     🟢 快速通道     🔵 标准通道     📥 需求池
    (Fast Track)  (Standard Track)  (Backlog)
            │            │            │
            │   阶段 3: 版本计划        │  积累打磨
            │            │            │
            ▼            ▼            │
       阶段 4-6      阶段 4-6          │
     直接实施发布   按版本实施发布        │
                                       │
                              └──── 选取进入阶段 3 ──▶
```

### 轨道对比

| 维度 | 🟢 快速通道 | 🔵 标准通道 | 📥 需求池 |
|------|-----------|-----------|----------|
| **适用场景** | 小增补，立即要 | 中大型变更，需规划 | 暂不实施，积累打磨 |
| **判定条件** | ≤ 2 文件、无数据迁移、无新实体 | 不满足快速条件 | 用户明确表示"先记下来" |
| **版本计划（阶段 3）** | 跳过 | 完整执行 | 暂不触发 |
| **plan/to-do/** | 不产生 | 产生 Plan 文件 | 不产生 |
| **门禁** | 1(轻量) + 2 + 4 + 5 | 全部 5 道 | 无（仅记录） |
| **版本号** | PATCH 递增 | 语义化版本 | 无 |
| **可追溯** | requirements + scenario + qa/reports + change-log | 完整追溯链 | backlog 条目 |
| **QA Agent** | 轻量验证（合并验证 + 冒烟） | 完整验证（逐 Story + 安全/压力测试） | 不涉及 |

### 分流决策

阶段 2 完成后，需求分析Agent 执行分流判断：

1. **是否立即实施？** — 用户是否明确要求现在做？
   - 否 → 📥 需求池（产出 backlog 条目）
   - 是 → 继续判断

2. **是否满足快速通道条件？** — 全部满足才可走快速通道：
   - [ ] 变更文件数 ≤ 2
   - [ ] 无数据迁移（不改已有数据文件的字段结构）
   - [ ] 无新实体（不新增 define/ 下的实体定义文件）
   - [ ] 无破坏性变更（不影响已有 API/命令签名）

   - ✅ 全部满足 → 🟢 快速通道
   - ❌ 任一项不满足 → 🔵 标准通道

3. **告知用户**分流结果和理由，用户确认后执行。

> **快速通道 ≠ Hotfix**：快速通道是计划内小变更的轻量路径，Hotfix 是紧急修复的例外流程（见下文「异常流程」章节）。Hotfix 保留端到端的灵活性，快速通道仍需完整的需求记录和测试。

---

## 需求池（Backlog）

需求池是标准通道的"蓄水池"——尚未排期的需求在此积累、打磨，时机成熟后被选取进入版本计划。

### 存储位置

`requirements/backlog.md` — 单一文件，所有未排期条目按时间倒序排列。

### 条目格式

```markdown
### B-{NNN}: {简短标题}

**状态**: draft / refined / ready
**优先级**: P0(紧急) / P1(高) / P2(中) / P3(低)
**复杂度**: 🟢 小 / 🟡 中 / 🔴 大
**创建日期**: YYYY-MM-DD
**关联场景**: S-NNN（如有）
**关联需求**: REQ-YYYYMMDD-NN（如已写入 requirements.md）

**一句话描述**: {功能边界一句话}

**初步方案思路**: {2-3 句话，可选}

**备注**: {用户的原话、约束条件、讨论要点}
```

### 状态流转

```
draft ──▶ refined ──▶ ready ──▶ 选取进入阶段 3 ──▶ done（从 backlog 移除）
  │                      │
  └──── 废弃（删除条目）  └──── 降级回 refined（方案需要更多思考）
```

- **draft**: 刚记下的原始想法，未分析
- **refined**: 已完成需求分析和方案设计，待排期
- **ready**: 方案成熟，优先级明确，可被版本计划选取
- **done**: 已进入版本计划，从 backlog 移除（不删除，移至 `requirements/backlog.md` 末尾「已交付」分区，或直接移除条目并在版本 Plan 中引用）

### 精炼操作

需求分析Agent 可在以下时机精炼 backlog 条目：

- 用户主动要求"整理需求池"
- 用户对某个 backlog 条目补充了新的想法
- 版本规划前，扫描 backlog 中 refined 条目，评估是否可提升为 ready

精炼过程无需走完整门禁。从 draft → refined 需要基本的需求澄清和方案可行性判断；从 refined → ready 需要用户明确确认优先级和排期意向。

### 版本规划选取

进入阶段 3 时，规划Agent 首先扫描 `requirements/backlog.md` 中状态为 `ready` 的条目，向用户提议可纳入当前版本的候选项。用户确认选取后，这些条目进入 3.1 Goal→Epic→Story 拆解流程。

选取后立即更新 backlog 条目状态：若直接进入版本计划，标记为 done 并注明版本号；若需要更多细化，保持 ready。

---

## 全生命周期概览

本方法论采用「需求分析 → 分流决策 → 按轨交付」模型：

- **需求分析**（阶段 1-2）：所有变更的公共入口——需求澄清、方案设计、门禁 1+2
- **分流决策**（阶段 2 出口）：根据变更规模和紧迫性，派发到快速通道/标准通道/需求池
- **按轨交付**（阶段 3-6）：标准通道经版本计划（阶段 3）→ 实施（阶段 4）→ QA 验证（阶段 5）→ 发布（阶段 6）；快速通道跳过阶段 3，经轻量验证后发布

需求分析Agent 负责需求分析 + 方案设计 + 分流决策；规划Agent 负责版本计划；交付Agent 负责实施 + 发布；QA Agent 负责独立验证 + 环境管理。标准通道下四者通过 `plan/to-do/` 和 `plan/verify/` 队列解耦，可并行推进。

### 需求分析Agent 流水线（需求分析 + 方案设计 + 分流）

```
需求输入
  │
  ▼
┌─────────────────────────────────────────────────────────────┐
│  阶段 1: 需求分析                                             │
│  ├─ 需求澄清 → 分类 → 风险识别 → 验收标准定义                    │
│  └─ 产出: requirements.md 更新、scenario.md 更新                  │
└────────────────────────────┬────────────────────────────────┘
                             │
                     🔒 门禁 1: 需求确认
                     （用户确认需求内容+成功标准）
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│  阶段 2: 解决方案设计                                         │
│  ├─ define/ → data/ → src/ 逐层设计                           │
│  ├─ 影响分析 → ADR（如需）                                     │
│  └─ 产出: 详细设计方案、受影响文件清单                            │
└────────────────────────────┬────────────────────────────────┘
                             │
                     🔒 门禁 2: 方案确认
                     （用户确认方案+影响范围）
                             │
                             ▼
                    ┌─────────────────┐
                    │   分流决策       │
                    │  (≤2文件？无迁移？ │
                    │   无新实体？)     │
                    └───┬─────┬─────┬─┘
                        │     │     │
            ┌───────────┘     │     └──────────────┐
            ▼                 ▼                    ▼
     🟢 快速通道         🔵 标准通道           📥 需求池
     (跳过阶段 3)       (进入阶段 3)         (积累打磨)
            │                 │                    │
            │                 ▼                    │
            │  ┌─────────────────────────────┐     │
            │  │  阶段 3: 版本计划            │     │
            │  │  ├─ 扫描 backlog ready 条目  │     │
            │  │  ├─ Goal→Epic→Story 拆解    │     │
            │  │  ├─ 估算→依赖→版本划分       │     │
            │  │  └─ 产出: plan/to-do/v{N}/  │     │
            │  └────────────┬────────────────┘     │
            │               │                       │
            │       🔒 门禁 3: 计划确认              │
            │       （用户确认拆分+版本划分）         │
            │               │                       │
            │               ▼                       │
            │  ┌──────────────────────────┐         │
            │  │    plan/to-do/v{N}/      │         │
            │  │    (规划→交付 交接队列)    │         │
            │  └────────────┬─────────────┘         │
            │               │                       │
            ▼               ▼                       │
      直接进入阶段 4    进入阶段 4                   │
                                                      │
                                          ┌── 选取进入阶段 3 ──┘
```

### 交付Agent + QA Agent 流水线（阶段 4-6，按版本循环）

```
              ┌──────────────────────────┐
              │    plan/to-do/v{N}/      │
              │    (拉取待实施版本)         │
              └────────────┬─────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  阶段 4: 实施（按版本循环）— 交付Agent                          │
│  ├─ 冲突检查 → 执行排序 → 逐个 Story 实施 + 代码自审             │
│  └─ 产出: 代码变更、plan/verify/v{N}/（交付→QA 交接）           │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │  plan/verify/v{N}/
                             │  (交付→QA 交接队列)
                             ▼
┌─────────────────────────────────────────────────────────────┐
│  阶段 5: 独立验证（按版本循环）— QA Agent                      │
│  ├─ 环境准备 → 定向验证 → 冒烟测试 → 安全/压力测试（按需）       │
│  └─ 产出: plan/done/v{N}/、qa/reports/、qa/metrics.md       │
└────────────────────────────┬────────────────────────────────┘
                             │
                     🔒 门禁 4: 质量确认
                     （QA Agent 判定：定向验证 + 冒烟测试全绿）
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│  阶段 6: 记录与发布（按版本循环）— 交付Agent                     │
│  ├─ CLAUDE.md 更新 → change-log → Git commit                  │
│  └─ 产出: 可追溯的版本记录                                      │
└────────────────────────────┬────────────────────────────────┘
                             │
                     🔒 门禁 5: 发布确认
                     （文档同步 + Git 提交完整）
                             │
                             ▼
                  ✅ 版本发布完成，继续下一版本
                           │
                           └──── 循环：拉取 plan/to-do/ 中下一版本 ──▶
```

详细流程定义在本 Skill 目录的以下文件中：

| 覆盖阶段 | Skill 文件 | 归属 Agent | 职责 |
|---------|-----------|-----------|------|
| 阶段 1-2 | `analyst/requirements-analysis.md` | 需求分析Agent | 需求澄清 → 方案设计 → 分流决策 |
| 阶段 3 | `planner/version-planning.md` | 规划Agent | 从需求池选取 + Goal→Epic→Story 拆分 + Release Version 划分 |
| 阶段 4 + 6 | `deliverer/implementation.md` | 交付Agent | 快速通道或标准通道：实施 → 记录发布 |
| 阶段 5 | `qa/quality-assurance.md` | QA Agent | 独立验证：定向验证 + 冒烟测试 + 安全/压力测试 + 质量度量 |
| — | `pmo/reporting.md` | PMO Agent | 扫描项目数据源，生成/更新 `pmo/dashboard.md` |

> 以上文件位于本 Skill 目录下的 `references/analyst/`、`references/planner/`、`references/deliverer/`、`references/qa/`、`references/pmo/` 子目录中，与 `methodology.md` 等位于 `references/` 下。各 Agent 分别加载对应文件执行任务。

---

## 五Agent协作模型

传统串行流程（需求→方案→计划→实施→验证→发布）在实际产品开发中存在瓶颈：规划和实施互相阻塞，测试质量依赖开发者自律。引入 AI 后，将"需求分析""版本计划""实施""验证""监控"拆分为五个独立 Agent：

- **需求分析Agent** 产出需求文档和方案设计，分流决策后交给规划Agent 或交付Agent
- **规划Agent** 与 **交付Agent** 通过 `plan/to-do/` 队列异步协作
- **交付Agent** 与 **QA Agent** 通过 `plan/verify/` 队列异步协作
- **PMO Agent** 消费前三者的产出物，生成项目状态看板

### 架构

```
需求分析Agent (Analyst)                    规划Agent (Planner)
  阶段 1-2                                    阶段 3
  ┌──────────────┐                       ┌──────────────┐
  │ 需求澄清      │─── requirements/ ────▶│ 扫描 backlog  │
  │ 方案设计      │   testing/            │ Goal→Epic     │
  │ 分流决策      │   (共享数据源)         │ →Story 拆解   │
  │              │                       │ 版本划分      │
  │ 产出:        │                       │              │
  │ requirements/│                       │ 产出:         │
  │ testing/     │                       │ plan/to-do/   │
  └──────────────┘                       │ versions/     │
       ▲      │                          └──────┬────────┘
       │      │                                 │
       │      │                          plan/to-do/
  门禁 1-2     │                          (规划→交付 队列)
       │      │                                 │
       │      │                                 ▼
       │      │                    ┌──────────────────────┐
       │      │                    │ 交付Agent (Delivery)  │
       │      │                    │   阶段 4 + 6          │
       │      │                    │ ┌──────────────────┐ │
       │      │                    │ │ 阶段 4: 实施      │ │
       │      │                    │ │  └─ 代码变更      │ │
       │      │                    │ │  └─ handoff       │ │
       │      │                    │ │                   │ │
       │      │  plan/verify/v{N}/ │ │ 阶段 6: 发布      │ │
       │      │  ◀── 交付→QA ───▶  │ │  └─ 文档同步      │ │
       │      │                    │ │  └─ Git commit    │ │
       │      │                    │ │                   │ │
       │      │                    │ │ 产出:              │ │
       │      │                    │ │ src/              │ │
       │      │                    │ │ data/             │ │
       │      │                    │ │ plan/verify/      │ │
       │      │                    │ │ change-log/       │ │
       │      │                    │ │ versions/         │ │
       │      │                    │ └──────────────────┘ │
       │      │                    └──────────┬───────────┘
       │      │                               │
       │      │                       ┌───────┴──────────┐
       │      │                       │   QA Agent        │
       │      └─── plan/verify/ ──────│   阶段 5: 独立验证 │
       │           (QA 失败回退)       │  ├─ 环境管理       │
       │                              │  ├─ 定向验证       │
       │                              │  ├─ 冒烟测试       │
       │                              │  ├─ 安全/压力测试   │
       │                              │  └─ 质量度量       │
       │                              │                   │
       │                                │ 产出:             │
       │                                │ qa/reports/        │
       │                                │ plan/done/         │
       │                                │ qa/metrics.md      │
       │                                └────────┬───────────┘
       │                                         │
       │                                ┌────────┴───────────┐
       │                                │   PMO Agent         │
       └────────────────────────────────│    (监控)           │
                                        │  只读扫描:          │
                                        │ requirements/       │
                                        │ plan/to-do/         │
                                        │ plan/verify/        │
                                        │ plan/done/          │
                                        │ versions/           │
                                        │ change-log/         │
                                        │ qa/                 │
                                        │                    │
                                        │ 产出:              │
                                        │ pmo/dashboard.md   │
                                        └────────────────────┘
```

### 职责边界

| 维度 | 需求分析Agent | 规划Agent | 交付Agent | QA Agent | PMO Agent |
|------|-------------|----------|----------|---------|-----------|
| **范围** | 阶段 1-2（需求分析、方案设计、分流决策） | 阶段 3（版本计划） | 阶段 4 + 6（实施、记录发布） | 阶段 5（独立验证、环境管理） | 项目状态监控、看板维护 |
| **可写** | `requirements/`、`define/`（含 `adr/`）、`data/enum-dictionary.json` | `plan/to-do/`、`versions/` | 源码目录、`data/`（业务数据）、`data/.backup/`、`plan/verify/`、`change-log/`、`CLAUDE.md` | `qa/reports/`、`qa/metrics.md`、`plan/done/`、`envs/*/sandbox/`、`envs/*/data/`（测试数据）、`envs/*/config/`（测试环境）、`testing/test-case.md`（起草）| `pmo/dashboard.md` |
| **只读** | 源码目录、`data/<业务数据文件>`、`testing/` | `requirements/`、`testing/`、`define/`、`requirements/backlog.md` | `define/`、`requirements/`、`testing/`、`plan/to-do/` | `plan/verify/`、`plan/to-do/`、`requirements/`、`testing/`、`define/`、源码目录 | `requirements/`、`plan/*/`、`versions/`、`change-log/`、`qa/` |
| **禁止碰** | 源码目录的代码实现；`data/` 中业务数据的修改 | 源码目录的代码实现 | `define/` 的数据结构定义；`requirements/` 的需求文档 | 源码目录的代码实现；`define/` 的结构定义；`requirements/` 的需求文档；`data/` 中生产数据的修改 | `define/`、`data/`、源码目录、`CLAUDE.md` |
| **门禁** | 门禁 1、2 | 门禁 3 | 门禁 5 | 门禁 4 | 无（纯报表职能） |

### 协作模式

- **需求分析先行**：需求分析Agent 完成阶段 1-2（需求分析 + 方案设计 + 分流决策），产出需求文档。标准通道下移交规划Agent 进入阶段 3
- **规划异步**：规划Agent 从需求分析Agent 接手后，完成阶段 3（Goal→Epic→Story 拆分），将 Plan 文件写入 `plan/to-do/v{N}/`，不等待交付Agent
- **异步交付**：交付Agent 从 `plan/to-do/` 拉取待实施版本，按 Story 逐个实施，完成后移入 `plan/verify/v{N}/`（向 QA 交接）
- **独立验证**：QA Agent 从 `plan/verify/` 拉取待验证 Story，在 `envs/qa/` 中执行验证，通过后移入 `plan/done/`
- **缺陷回退**：QA Agent 验证失败时，将 Plan 文件移回 `plan/to-do/` 并附失败报告，交付Agent 修复后重新移交
- **并行推进**：QA Agent 验证 v1 的同时，交付Agent 可以实施 v2，规划Agent 可以准备 v3，需求分析Agent 可以分析新需求
- **下游监控**：PMO Agent 在各 Agent 产出后独立运行，扫描所有数据源生成看板
- **队列驱动**：`plan/to-do/` 是规划→交付 交接面，`plan/verify/` 是交付→QA 交接面

### 跨Agent一致性保证

定义层 → 数据层 → 代码层 → 测试层 四层在 Agent 模型下被拆分。一致性通过 `plan/to-do/` 和 `plan/verify/` 中的 Plan 文件作为**契约**来保证：

- **需求分析Agent** 在需求文档中明确定义变更目标和方案：数据结构、字段、枚举值、体验收标准
- **规划Agent** 在 Plan 文件中承接需求分析输出，进一步细化为：Story 拆分、测试用例编号、依赖关系
- **交付Agent** 按 Plan 文件的契约实施代码和数据层，不自行偏离
- **QA Agent** 按 Plan 文件中列出的测试用例编号执行验证，验证结果计入 `qa/reports/`
- 交付Agent 实施时发现 Plan 无法落地，应暂停并反馈规划Agent 修订后继续
- QA Agent 发现测试用例不覆盖实际变更，应自行补充测试用例并告知需求分析Agent
- **PMO Agent** 不参与契约，仅消费最终产出

### 版本号分配

版本号由规划Agent在阶段3分配（见语义化版本规则），交付Agent在阶段6确认。若实施中发现需要调整版本号（如发现额外变更导致破坏性升级），交付Agent应与规划Agent沟通确认。

### 启动方式

五个 Agent 通过独立的 Claude Code 会话并行运行：

1. **需求分析Agent**：用户说明新需求，Skill `agile-dev` 自动激活，从阶段 1 推进到阶段 2，产出需求文档和方案设计
2. **规划Agent**：需求分析Agent 完成标准通道移交后，用户启动规划会话，Skill `agile-dev` 自动激活，进入阶段 3，产出 Plan 文件
3. **交付Agent**：用户指定要交付的版本号或小变更，Skill `agile-dev` 自动激活，从阶段 4 开始
4. **QA Agent**：交付Agent 完成移交后，用户启动 QA 会话，Skill `agile-dev` 自动激活，从阶段 5 开始；或用户主动要求执行测试/管理环境
5. **PMO Agent**：用户询问项目状态或版本发布后，Skill `agile-dev` 自动激活，生成/更新看板

> 实际操作：开多个终端窗口，需求分析、规划、交付、QA、PMO 在各自会话中独立运行，互不阻塞。

---

## 角色定义

即便是单人项目，也需在迭代过程中切换以下角色视角，确保多维度思考。五个 Agent 可在独立会话中并行运行。

### 职能角色

在 Agent 内部，仍需切换以下职能视角：

| 角色 | 职责 | 在哪个阶段重点出现 |
|------|------|-------------------|
| **产品负责人（PO）** | 定义需求、排优先级、验收结果 | 阶段 1（需求分析Agent）、3（规划Agent） |
| **开发者（Dev）** | 方案设计、编码实现 | 阶段 2（需求分析Agent）、4（交付Agent） |
| **质量保证（QA）** | 测试设计（与需求分析协作）、独立验证、环境管理、质量门禁判定 | 阶段 1（协作设计用例）、5（独立验证，QA Agent） |
| **运维（Ops）** | 数据安全、向后兼容、发布记录 | 阶段 4（交付Agent）、6（交付Agent） |
| **PMO** | 项目状态汇总、看板维护、异常预警 | 阶段 6 完成后（自动）、按需（PMO Agent） |

> 角色冲突警示：当同一个决策从 PO 视角看合理，但从 Dev 视角看有隐患时，必须优先考虑数据安全和向后兼容原则。

---

## 贯穿标准

编码规范、设计原则、测试策略、沙箱隔离规则见 [`design-principles.md`](design-principles.md)。

## 定义完成（DoD）与质量度量

各层级完成标准和质量指标见 [`dod-and-qa.md`](dod-and-qa.md)。

## 风险管理与异常流程

风险管理框架、ADR、Hotfix、回滚策略、禁止事项见 [`risk-and-exceptions.md`](risk-and-exceptions.md)。

---

## 可追溯矩阵

每个变更必须满足以下追溯链。标准通道走完整链路，快速通道跳过 `plan/to-do/` 环节（标注为可选）：

```
requirements/requirements.md（需求条目 + 修订记录）
  │  需求编号: REQ-YYYYMMDD-NN
  ▼
requirements/scenario.md（业务/运维场景）
  │  场景编号: B-NN（业务）/ O-NN（运维）
  ▼
testing/test-case.md（测试用例）
  │  用例编号: TC-BNN / TC-ONN（与场景编号对应）
  ▼
plan/to-do/v{N}/E{N}-S{N}-{描述}.md（实施计划 — 标准通道）
  │  Story 中标注影响的场景编号和测试用例编号
  ▼
源码 + data/（代码变更 + 数据变更）
  │  交付Agent 产出，移入 plan/verify/
  ▼
qa/reports/v{x.y.z}-report.md（QA 验证报告 — QA Agent 产出）
  │  定向验证结果、冒烟测试结果、环境验证结果
  ▼
change-log/YYYY-MM-DD-v{x.y.z}-{描述}.md（变更记录）
  │
  ▼
versions/v{x.y.z}.md → versions/roadmap.md（版本发布说明 → 路线图汇总）
```

> 快速通道：`plan/to-do/` 环节跳过，其余链路不变。追溯检查：从任意一个环节向上下游追溯，应能定位到对应条目。

---

## 门禁适用矩阵

5 道门禁对每条轨道的适用情况：

| 门禁 | 🟢 快速通道 | 🔵 标准通道 | 🔥 Hotfix | 📥 需求池 | **执行Agent** |
|------|:--:|:--:|:--:|:--:|:--:|
| 门禁 1: 需求确认 | ✅ 轻量 | ✅ 完整 | ✅ 事后补充 | — | 需求分析Agent |
| 门禁 2: 方案确认 | ✅ | ✅ | ❌ | — | 需求分析Agent |
| 门禁 3: 计划确认 | ❌ | ✅ | ❌ | — | 规划Agent |
| 门禁 4: 质量确认 | ✅ | ✅ | ✅ | — | **QA Agent** |
| 门禁 5: 发布确认 | ✅ | ✅ | ✅ | — | 交付Agent |

- **✅ 轻量**：保留核心检查项，流程从简（如快速通道门禁 1 只需确认范围和验收标准）
- **✅ 完整**：全部检查项通过
- **✅ 事后补充**：实施后补全（Hotfix 事后补需求和测试用例）
- **❌**：不适用
- **—**：不进入该阶段（需求池仅做记录，不走门禁）

---

## 多环境与 Git 分支

### 环境与分支映射

| 环境 | 目录 | Git 分支 | 用途 | 管理者 |
|------|------|---------|------|--------|
| **qa** | `envs/qa/` | `qa` | 功能测试，日常开发集成 | QA Agent（测试）、交付Agent（开发） |
| **stg** | `envs/stg/` | `stg` | 预发布验证，UAT | QA Agent |
| **prod** | `envs/prod/` | `prod` / `main` | 生产环境 | 交付Agent |
| **pen-test** | `envs/pen-test/` | `pen-test` | 安全渗透测试 | QA Agent |
| **stress-test** | `envs/stress-test/` | `stress-test` | 负载/压力测试 | QA Agent |

### 代码晋升流程

```
qa ──(Gate 4 通过)──▶ stg ──(UAT 通过)──▶ prod (main)
 │
 ├── pen-test    (从 qa 分出，QA Agent 独立管理，不上游合并)
 └── stress-test (从 qa 分出，QA Agent 独立管理，不上游合并)
```

**规则**：
- 日常开发在 `qa` 分支，所有功能分支合并到 `qa`
- `qa` → `stg`：QA Gate 4 通过后，由 QA Agent 执行 `git checkout stg && git merge qa`
- `stg` → `prod`：UAT/预发布验证通过后，由交付Agent 执行 `git checkout prod && git merge stg` + 阶段 6
- `pen-test` 和 `stress-test` 从 `qa` 分支分出，QA Agent 独立维护，**不上游合并**
- **禁止** `qa` → `prod` 直接合并（必须经 `stg`）

---

## 持续改进

### 轻量回顾（Retrospective）

每个 Release Version 的 Git commit 完成后，执行一次轻量回顾（5 分钟即可）：

1. **What went well?** — 本次迭代中哪个环节最顺畅？为什么？
2. **What was painful?** — 哪个环节遇到了摩擦？是否有工作绕过流程？
3. **What to change?** — 是否有流程步骤需要调整？是否需要更新本方法论？

回顾结论写入 `change-log/` 变更记录的末尾，格式：

```markdown
## 回顾
- ✅ {做得好的点}
- ⚠️ {摩擦点}
- 🔧 {改进建议}（如有）
```

若改进建议涉及方法论本身，**立即修订本方法论文件**，不让改进点流失。
