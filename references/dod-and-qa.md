# 定义完成与质量度量

> 各层级的完成标准（DoD）与质量指标体系。交付Agent 在阶段 4-5 中严格执行，PMO Agent 在看板中汇总。

## 定义完成（Definition of Done）

### Story 级 DoD

- [ ] 所有子任务完成
- [ ] 定向验证测试全部通过
- [ ] 代码符合 `CLAUDE.md` 定义的编码规范和设计原则
- [ ] Plan 文件已移动到 `plan/done/`

### Version 级 DoD

- [ ] 所有 Story 完成（各自满足 Story 级 DoD）
- [ ] 冒烟测试 100% 通过
- [ ] `CLAUDE.md` 已更新
- [ ] 变更记录已写入 `change-log/`
- [ ] Git commit 已创建，message 格式 `v{版本号}: {简短描述}`
- [ ] 轻量回顾已完成（见 `../methodology.md` 持续改进章节）

### Iteration 级 DoD

- [ ] 所有 Release Version 完成（各自满足 Version 级 DoD）
- [ ] `plan/to-do/` 中本轮迭代的版本已清空（全部移入 `plan/done/`），即所有交付物已完成
- [ ] 需求方验收通过（PO 角色确认）
- [ ] 技术债务已记录（如有，记入 `requirements/requirements.md`）
- [ ] 迭代回顾已完成

## 质量度量

每个 Release Version 完成后记录以下指标（写入 `change-log/` 变更记录）：

| 指标 | 计算方式 | 目标值 |
|------|---------|--------|
| 测试通过率 | 通过用例数 / 总用例数 | = 100% |
| 场景覆盖率 | 有测试用例覆盖的场景数 / 总场景数 | ≥ 90% |
| 变更失败率 | 需要回滚或紧急修复的版本数 / 总版本数 | = 0% |
| 定向验证效率 | Story 完成到定向验证通过的时间 | 同一次会话内 |
