# 项目初始化指南

> 项目接入本方法论的操作指南：覆盖全新项目（含无 Git）和存量项目接入的场景。治理规则见 [`governance/project-init.md`](../governance/project-init.md)。
>
> 读者是人（项目初始化者）。系统会在 Skill 激活时加载此文件并代为执行初始化，但文件本身面向人的阅读理解。

---

## 接入场景速览

在开始之前，判断你的项目属于哪个场景：

| 场景 | 判断方法 | 你的下一步 |
|------|---------|-----------|
| 🆕 **全新项目（有 Git）** | 项目目录已 `git init`，目录为空或仅有初始文件 | 直接跳到 [project-basic.md 模板](#project-basicmd-模板) |
| 🔧 **全新项目（无 Git）** | 项目在一个普通目录中，尚未执行 `git init` | 先看 [如果你还没有 Git 仓库](#如果你还没有-git-仓库) |
| 📦 **存量项目接入** | 项目已有代码、数据文件、Git 提交历史 | 先看 [存量项目接入特别说明](#存量项目接入特别说明) |

---

## 如果你还没有 Git 仓库

如果你的项目目录尚未执行 `git init`，按以下步骤完成初始化：

### 第一步：初始化 Git 仓库

在项目根目录执行：

```bash
git init
```

### 第二步：创建 .gitignore

至少排除以下内容（根据实际技术栈调整）：

```bash
# 系统文件
.DS_Store
Thumbs.db

# 环境目录（模式 A 目录隔离）
envs/

# IDE 配置
.vscode/
.idea/
```

### 第三步：创建初始提交（建议）

```bash
git add -A
git commit -m "chore: 项目初始化"
```

> 初始提交不是硬性要求，但建议在接入方法论前至少有一个 commit，便于后续追溯。

Git 仓库就绪后，继续按 [project-basic.md 模板](#project-basicmd-模板) 操作。

---

## project-basic.md 模板

在项目根目录创建 `project-basic.md`，复制以下模板并填写。

> 📦 **存量项目注意**：模板中标注了「📦 存量」的章节，在存量项目接入时有不同的填写方式——核心原则是**描述现状，不预设未来**。详见 [模板填写差异](#模板填写差异)。

```markdown
# 项目基本信息

## 项目标识

- **名称**: 项目名称
- **根目录**: /absolute/path/to/project

## 功能模块

- **branch-management**:
  - enabled: false
  - remote: git@github.com:user/repo.git  # enabled=true 时必填
- **security-testing**:
  - enabled: false
- **stress-testing**:
  - enabled: false

## 战略主题（按需）

（可选）声明当前项目的活跃战略主题（Initiatives），用于跨版本聚合需求。需求分析Agent 写入 backlog 条目时从此列表选取。

- **{initiative-key}**: {简短描述}
  - 如 `auth-system`: 用户认证体系（登录、权限、OAuth）

> 若项目暂无跨版本主题，可省略此节。

## 目录结构

（当前项目的目录树概览，可简化）

## 技术栈

- 编程语言与版本
- 运行环境
- 依赖约束

## 数据模型

（按需。无持久化数据的项目可省略此节）

### 核心实体

- **{实体名}**: {描述}
  - 字段说明（名称、类型、必填/可选、默认值）
  - 关联关系（与其它实体的引用关系）

## 关键规则

- 业务规则
- 设计约束
- 禁止事项

## 命令/API 参考

- 可执行命令或 API 端点列表
```

### 章节说明

| 章节 | 必填 | 说明 |
|------|------|------|
| `## 项目标识` | 是 | `名称` 用于看板标题与报告标识；`根目录` 为项目绝对路径 |
| `## 功能模块` | 是 | `branch-management` 控制环境隔离方式（分支 or 目录）；`security-testing` 控制安全测试；`stress-testing` 控制压力测试 |
| `## 战略主题` | 按需 | 活跃的跨版本主题（Initiatives），需求分析Agent 填写 backlog 条目 `initiative` 字段时从此列表选取 |
| `## 目录结构` | 是 | 当前项目的目录树，Agent 据此了解文件布局。📦 存量项目填写实际目录树，包括已有源码目录、数据目录等 |
| `## 技术栈` | 是 | 编程语言、运行环境、依赖约束。📦 存量项目如实填写当前使用的技术和版本，包括已有第三方依赖 |
| `## 数据模型` | 按需 | 核心实体、字段、关联关系；无持久化数据的项目可省略。📦 存量项目描述已有的数据结构和字段 |
| `## 关键规则` | 是 | 业务规则、设计约束、禁止事项。📦 存量项目填写当前项目中已存在的规则和约束 |
| `## 命令/API 参考` | 是 | 可执行命令或 API 端点列表，Agent 据此执行操作。📦 存量项目列出已有的运行/构建/测试命令 |

### 功能模块说明

**`branch-management`** — 控制环境隔离方式：

| 值 | 隔离方式 | 必需配置 |
|----|---------|---------|
| `enabled: true` | Git 分支隔离（一个环境 = 一个分支，切换分支即切换环境） | `remote` 必填 |
| `enabled: false` | 目录隔离（一个环境 = 一个 `envs/{env}/` 目录，各有独立 `data/` + `config/`） | — |

**选择建议**：
- 选择**目录隔离**（默认）：项目在本地单人开发，不需要多机协作；或希望快速切换环境（cd 即可，无需 git checkout）。推荐大多数个人项目使用此模式。
- 选择**分支隔离**：多人协作需要独立的 Git 历史；或环境切换需要同步代码+数据变更（`git checkout` 同时切换代码和数据）。需要配置 `remote`。

**`security-testing`** — 启用后创建 pen-test 环境（QA Agent 执行安全测试）；未启用则 QA Agent 跳过安全测试环节。

**`stress-testing`** — 启用后创建 stress-test 环境（QA Agent 执行压力测试）；未启用则 QA Agent 跳过压力测试环节。

---

## 目录初始化流程

当你创建好 `project-basic.md` 后，系统会在首次激活时自动读取该文件并按模块开关完成目录初始化（创建所需目录、环境、模板文件）。以下是初始化的具体规则和执行命令，供你了解系统会做什么——你无需手动执行这些命令。

### 初始化规则

1. **读取配置** — 解析 `project-basic.md`，确定模式 A/B 以及 `security-testing`、`stress-testing` 开关状态
2. **创建公共目录** — `define/adr`、`data/.backup`、`requirements/backlog`、`change-log`、`versions`、`plan/to-do`、`plan/verify`、`plan/done`、`pmo`、`qa/function-test-reports`、`function-testing`
3. **按模块追加** — `security-testing.enabled` → `qa/pen-test-reports`；`stress-testing.enabled` → `qa/stress-test-reports`
4. **创建环境** — 模式 A：核心 5 环境 (dev/qa/stg/prod/hotfix) + 按模块追加 (pen-test/stress-test)；模式 B：核心 5 分支 + 按模块追加
5. **创建空白模板文件** — `qa/metrics.md`、`pmo/dashboard.md`、`requirements/requirements.md`、`requirements/scenario.md`

### 执行规则

- 必须在读取 `project-basic.md` 之后、进入需求分析Agent 流程之前完成
- 目录和文件创建命令幂等，可重复执行
- 若 `remote` 未配置（模式 B），中止流程并告知用户
- 若任一创建操作失败（如权限不足），中止流程并告知用户

### 模式 A 初始化命令（全新项目 🆕/🔧）

以下命令适用于全新项目，在空目录中创建所有标准目录。

```bash
# 公共目录
mkdir -p define/adr data/.backup requirements/backlog change-log versions \
  plan/to-do plan/verify plan/done pmo qa/function-test-reports function-testing

# 环境目录
mkdir -p envs/dev/{data,config} envs/qa/{data,config} envs/stg/{data,config} \
  envs/prod/{data,config} envs/hotfix/{data,config}

# 按模块追加（security-testing 启用时）
mkdir -p qa/pen-test-reports envs/pen-test/{data,config}

# 按模块追加（stress-testing 启用时）
mkdir -p qa/stress-test-reports envs/stress-test/{data,config}
```

> 上述命令在项目根目录执行。如项目使用不同的源码目录名（如 `app/` 替代 `src/`），按 `project-basic.md` 中的实际目录结构调整。

### 模式 A 初始化命令（存量项目 📦）

存量项目的初始化命令与全新项目**完全相同**——`mkdir -p` 是幂等的，已存在的目录不会被覆盖。但需要注意：

```bash
# 存量项目唯一需要额外检查的：data/ 目录冲突
# 若项目已有 data/，初始化只补充 .backup 子目录
mkdir -p data/.backup

# 其余命令与全新项目完全相同，安全执行
mkdir -p define/adr requirements/backlog change-log versions \
  plan/to-do plan/verify plan/done pmo qa/function-test-reports function-testing \
  envs/dev/{data,config} envs/qa/{data,config} envs/stg/{data,config} \
  envs/prod/{data,config} envs/hotfix/{data,config}
```

> `mkdir -p` 保证不会覆盖已有目录和文件。执行后，标准目录与项目已有文件并存。

---

## 存量项目接入特别说明

> 先阅读 [`governance/project-init.md`](../governance/project-init.md) 了解 📦 场景的治理规则（5 条额外约束），再按本节操作。

### 盘点现有结构

在填写 `project-basic.md` 之前，先在项目根目录做一次快速盘点。花 5 分钟逐项过一遍：

| 盘点项 | 看什么 | 用于模板章节 |
|--------|--------|------------|
| **源码入口** | 主入口文件（`main.py`、`index.js` 等）、源码目录名（`src/`、`app/`、`lib/` 等） | `## 目录结构`、`## 命令/API 参考` |
| **数据文件** | `data/` 或类似目录下的 `.json`、`.db`、`.csv` 等文件 | `## 数据模型`、`## 目录结构` |
| **配置文件** | `.env`、`config.*`、`settings.*` 等 | `## 目录结构` |
| **依赖清单** | `requirements.txt`、`package.json`、`go.mod` 等——确认编程语言、版本、第三方依赖 | `## 技术栈` |
| **测试文件** | 已有的测试目录和测试框架 | `## 命令/API 参考` |
| **版本号** | `version` 文件、`CHANGELOG.md`、Git tag——当前版本号是什么 | 版本号沿用 |
| **Git 状态** | `git log --oneline -5`——最近的提交历史 | 确认无需 rebase |

盘点只读，不修改任何文件。目的是让你了解"我的项目现在长什么样"，然后如实填入模板。

### 模板填写差异

存量项目填写 `project-basic.md` 时，与全新项目的关键差异：

| 模板章节 | 🆕 全新项目 | 📦 存量项目 |
|---------|-----------|-----------|
| `## 项目标识` | 取一个新名字 | 使用项目已有的名字 |
| `## 目录结构` | 规划未来的目录布局 | 粘贴 `ls -la` 或 `tree -L 2` 的实际输出 |
| `## 技术栈` | 选择要用的技术 | 如实填写当前使用的语言、版本、已有依赖 |
| `## 数据模型` | 设计新的实体和字段 | 描述已有数据文件的结构——打开实际的 `.json` 或 `.db` 文件，列出字段名和类型 |
| `## 关键规则` | 定义新的规则 | 写下项目中已存在的显式或隐式规则（如"合同编号格式为 XXXX-YYYY"） |
| `## 命令/API 参考` | 规划命令接口 | 列出已有的运行命令（`npm start`、`python main.py`）、测试命令、构建命令 |

核心心法：**你是在给项目拍一张"现状快照"，不是在画一张"理想蓝图"**。Agent 会根据这个快照来理解你的项目，所以必须真实。

### 初始化后验证

存量项目初始化完成后，逐项确认：

- [ ] `project-basic.md` 已在项目根目录，内容反映项目实际状态
- [ ] 标准目录（`define/`、`requirements/`、`plan/`、`qa/`、`pmo/` 等）已创建在项目根目录
- [ ] `envs/` 目录已创建（模式 A），包含 5 个核心环境
- [ ] **已有文件和目录未被修改、移动或删除**——可用 `git status` 确认，只有新增文件，没有变更或删除
- [ ] Git 历史完整，未被 rebase 或 force push
- [ ] 项目原有的运行/构建/测试命令仍可正常执行

### 常见陷阱

存量项目接入时最容易踩的坑：

1. **❌ 在模板中写"应该用的技术栈"而非"实际用的技术栈"** — Agent 读到一个不存在的依赖会误判项目状态。如实写：Node.js 16 就是 Node.js 16，哪怕你想升到 20。

2. **❌ 移动已有文件到标准目录** — 不要因为方法论有 `data/` 就把你的 `db/` 目录重命名为 `data/`。保持原有结构，让标准目录与它共存。后续可以在 backlog 中记录"统一数据目录"作为技术债务。

3. **❌ 初始化后立即大规模重构** — 接入后第一个版本不要追求"让代码符合设计原则"。先让业务需求流转起来，技术债务逐版本消化。

4. **❌ 重写 Git 历史以"看起来从一开始就在用方法论"** — 没有必要。从接入点开始追溯即可，历史 commit 保持原样。

5. **❌ 强行补齐存量数据的逻辑删除字段** — 不要在初始化阶段修改数据文件。缺 `is_deleted`、`updated_at` 等字段的情况，记录到 backlog 条目，由后续版本处理。
