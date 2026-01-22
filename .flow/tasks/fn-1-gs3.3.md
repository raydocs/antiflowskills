# fn-1-gs3.3 移植核心技能 flow-next plan work interview

## Description

移植核心技能集: flow-next (任务管理), flow-next-plan (规划), flow-next-work (执行), flow-next-interview (访谈)。

**Size:** M
**Files:**
- `.agent/skills/flow-next/SKILL.md`
- `.agent/skills/flow-plan/SKILL.md` + `steps.md`
- `.agent/skills/flow-work/SKILL.md` + `phases.md`
- `.agent/skills/flow-interview/SKILL.md`

## Approach

1. 从源仓库 `plugins/flow-next/skills/flow-next-*/` 复制 SKILL.md 文件
2. 移除 Claude 特有字段: `context: fork`, `allowed-tools`, `disable-model-invocation`
3. 将 commands/*.md 的触发短语合并到 SKILL.md 的 description 字段
4. 更新路径引用（替换规则）:
   - `$CLAUDE_PLUGIN_ROOT` 作为目录 → `$(git rev-parse --show-toplevel)`
   - `$CLAUDE_PLUGIN_ROOT/scripts/flowctl` → `$(git rev-parse --show-toplevel)/.flow/bin/flowctl`
   - 其他引用 → 手动审查决定
5. 测试相对路径引用 (phases.md, steps.md) 在技能目录下可用

## Source Snapshot

- 源: `https://github.com/gmickel/gmickel-claude-marketplace`
- Commit: 需在 epic 中 pin 具体 SHA
- 目标: `.agent/skills/`

## Key context

- 保持 SKILL.md < 500 行
- description 字段是语义触发的关键
- 相对文件引用必须在技能目录内

## Acceptance

- [ ] flow-next 技能 SKILL.md 已创建
- [ ] flow-plan 技能 SKILL.md + steps.md 已创建
- [ ] flow-work 技能 SKILL.md + phases.md 已创建
- [ ] flow-interview 技能 SKILL.md 已创建
- [ ] 所有 `$CLAUDE_PLUGIN_ROOT` 引用已按替换规则处理
- [ ] flowctl 调用使用动态 repo root（repo 内任意 cwd 可用）
- [ ] 相对文件引用 (phases.md, steps.md) 在 Antigravity 中可加载

## Done summary

TBD

## Evidence

- Commits:
- Tests:
- PRs:
