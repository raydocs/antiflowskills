# fn-1-gs3.5 移植研究子代理 scouts

## Description

移植研究子代理 (scouts): context-scout, repo-scout, practice-scout, docs-scout, github-scout, memory-scout, epic-scout, flow-gap-analyst, docs-gap-scout。

**Size:** M
**Files:**
- `.agent/skills/context-scout/SKILL.md`
- `.agent/skills/repo-scout/SKILL.md`
- `.agent/skills/practice-scout/SKILL.md`
- `.agent/skills/docs-scout/SKILL.md`
- `.agent/skills/github-scout/SKILL.md`
- `.agent/skills/memory-scout/SKILL.md`
- `.agent/skills/epic-scout/SKILL.md`
- `.agent/skills/flow-gap-analyst/SKILL.md`
- `.agent/skills/docs-gap-scout/SKILL.md`

## Approach

1. 将源仓库 `agents/*.md` 转换为 `skills/*/SKILL.md` 格式
2. 移除 `context: fork` 依赖，接受共享上下文
3. 为每个 scout 定义最小工具集合要求
4. 添加工具降级策略（无工具时提供具体手动执行指引）
5. context-scout 保持 rp-cli builder 集成（macOS only，非 macOS 降级到 repo-scout）

## Tool Requirements per Scout

| Scout | 必需工具 | 降级策略 |
|-------|---------|----------|
| repo-scout | Grep, Glob, Read | 输出: "请执行 `grep -r 'pattern' .`" |
| context-scout | rp-cli (macOS) | 降级到 repo-scout |
| practice-scout | WebSearch | 输出: "请搜索: <query>" |
| docs-scout | WebSearch, WebFetch | 输出: "请访问: <URL list>" |
| github-scout | Bash (gh) | 输出: "请执行 `gh search code <query>`" |
| memory-scout | Read, Glob | 输出: "请检查 .flow/memory/ 目录" |
| epic-scout | Read, Glob | 输出: "请检查 .flow/epics/ 目录" |
| flow-gap-analyst | 无特殊工具 | 纯文本分析 |
| docs-gap-scout | Glob, Read | 输出: "请检查以下文件: <list>" |

## Key context

- 原始子代理在 `plugins/flow-next/agents/`
- Antigravity 无 `context: fork`，scouts 共享主上下文
- 这些技能主要被其他技能内部调用
- 工具存在性基于任务 1 的工具映射验证结果

## Acceptance

- [ ] 所有 9 个 scout 技能 SKILL.md 已创建
- [ ] 每个 scout 有明确的最小工具集合声明
- [ ] 每个 scout 有工具不可用时的降级策略，降级输出包含:
  - 用户需执行的具体命令
  - 命令参数填充说明
  - 预期输出格式
- [ ] 基于任务 1 工具映射结果:
  - 工具存在: 执行一次最小验证（如 repo-scout 执行一次搜索）
  - 工具不存在: 验证降级输出符合上述格式
- [ ] context-scout 在 macOS 有 rp-cli 时可用 builder；否则降级到 repo-scout

## Done summary

TBD

## Evidence

- Commits:
- Tests:
- PRs:
