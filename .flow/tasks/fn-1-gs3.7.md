# fn-1-gs3.7 更新用户文档和迁移指南

## Description

更新用户文档和创建迁移指南，包含 Antigravity 使用说明。

**Size:** S
**Files:**
- CLAUDE.md (更新)
- AGENTS.md (更新)
- .flow/usage.md (更新)
- docs/antigravity-migration.md (新建)

## Approach

1. 在 CLAUDE.md 添加 Antigravity 技能路径说明
2. 更新 AGENTS.md 支持双平台
3. 在 .flow/usage.md 添加 Antigravity 路径信息和 worktree 说明
4. 创建迁移指南文档，包含:
   - 功能对比表
   - 目录结构差异
   - 不支持的功能 (Ralph mode, hooks)
   - 平台支持矩阵 (macOS vs 其他)
   - 常见问题

## Key context

- 保持 HTML 注释标记 (`<!-- BEGIN/END FLOW-NEXT -->`)
- 文档应简洁，避免冗余
- 迁移指南面向已熟悉 Claude Code flow-next 的用户

## Acceptance

- [ ] CLAUDE.md 包含 Antigravity 使用说明
- [ ] AGENTS.md 支持双平台
- [ ] .flow/usage.md 包含 Antigravity 路径信息
- [ ] 所有文档包含动态 repo root 用法示例:
  ```bash
  REPO_ROOT="$(git rev-parse --show-toplevel)"
  "$REPO_ROOT/.flow/bin/flowctl" <command>
  ```
- [ ] .flow/usage.md 包含 worktree 使用说明
- [ ] 文档包含 rp-cli macOS-only 降级说明:
  - 明确说明 macOS 限制
  - 提供非 macOS 用户的替代方案/提示文案
- [ ] 迁移指南文档完整
- [ ] 功能差异清晰记录（包括 Ralph mode、hooks 不可用）

## Done summary

TBD

## Evidence

- Commits:
- Tests:
- PRs:
