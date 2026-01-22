# fn-1-gs3.4 移植审查技能 plan-review impl-review

## Description

移植审查技能: flow-plan-review (计划审查) 和 flow-impl-review (实现审查)。

**Size:** M
**Files:**
- `.agent/skills/flow-plan-review/SKILL.md` + `workflow.md`
- `.agent/skills/flow-impl-review/SKILL.md` + `workflow.md`

## Approach

1. 复制审查技能的 SKILL.md 和 workflow.md
2. 移除 hooks 依赖 (ralph-guard 相关)
3. 更新 RepoPrompt 调用路径 (`flowctl rp` 命令)
4. 简化审查流程为非阻塞模式 (无 receipt 强制)
5. 保持 Carmack-level 审查标准不变

## Key context

- 原始审查依赖 hooks 系统强制通过
- Antigravity 无 hooks，改为文档化的最佳实践
- rp-cli 为主（通过 `flowctl rp` 封装）；MCP 若支持则在任务 6 记录/验证
- 参考 flow-next-opencode 的审查实现

## Acceptance

- [ ] flow-plan-review SKILL.md + workflow.md 已创建
- [ ] flow-impl-review SKILL.md + workflow.md 已创建
- [ ] 审查标准 (Carmack-level) 保持不变
- [ ] 无 hooks 依赖，改为文档化流程
- [ ] RepoPrompt 调用使用 `flowctl rp` 封装（不直接依赖 MCP）

## Done summary

TBD

## Evidence

- Commits:
- Tests:
- PRs:
