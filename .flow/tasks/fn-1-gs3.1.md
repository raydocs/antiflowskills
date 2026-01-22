# fn-1-gs3.1 验证 Antigravity 环境和路径解析

## Description

验证 Antigravity 技能环境的基本配置、cwd 规则和工具映射。

**Size:** S
**Files:** 无新文件，仅验证和记录

## Approach

1. 在 Antigravity 环境中测试 `.agent/skills/` 目录识别
2. 验证技能执行时的 cwd 规则（是否在 repo root、skill 目录、或其他）
3. 测试 SKILL.md 中相对路径引用（如 `[phases.md](phases.md)`）在不同 cwd 下的行为
4. 验证可用工具集合，与 Claude Code 工具做映射对比
5. 记录发现到 `.flow/memory/antigravity-paths.md`

## Key context

- Antigravity 使用 Progressive Disclosure 加载技能
- 技能目录: workspace 级 `.agent/skills/`，全局 `~/.gemini/antigravity/skills/`
- cwd 可能不固定，需要验证
- 工具名称可能不同于 Claude Code

## Acceptance

- [ ] 确认 `.agent/skills/` 目录被 Antigravity 识别
- [ ] 记录技能执行时的 cwd 规则（明确：repo 内任意目录 vs 任意目录）
- [ ] 记录相对路径引用行为（哪些链接可工作、在什么 cwd 下）
- [ ] 完成工具映射表（Claude tools → Antigravity tools），并验证每个工具是否存在
- [ ] `.flow/memory/antigravity-paths.md` 包含验证结果
- [ ] 确认动态 repo root 解析策略可行（`git rev-parse --show-toplevel`）

## Done summary

TBD

## Evidence

- Commits:
- Tests:
- PRs:
