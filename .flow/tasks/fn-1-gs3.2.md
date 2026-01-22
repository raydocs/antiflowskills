# fn-1-gs3.2 创建基础目录结构和共享配置

## Description

创建 Antigravity 技能的基础目录结构，配置 flowctl 支持 repo 内任意 cwd 调用。

**Size:** S
**Files:**
- `.agent/skills/` (新目录)
- `.flow/bin/flowctl` (复制)
- `.gitignore` (更新)

## Approach

1. 创建 `.agent/skills/` 目录结构
2. 从源仓库复制 flowctl 到 `.flow/bin/`
3. 确保 flowctl 可执行权限
4. 更新 `.gitignore` 排除: rp-cli 输出、临时文件、Antigravity 生成物
5. 验证 flowctl 支持 repo 内任意 cwd 调用

## Key context

- flowctl 是 Python 脚本，无外部依赖
- 所有调用使用动态 repo root: `"$(git rev-parse --show-toplevel)/.flow/bin/flowctl"`
- 源仓库: `https://github.com/gmickel/gmickel-claude-marketplace` (需 pin 到具体 commit)
- "任意 cwd" 定义: repo 或 worktree 内的任意子目录

## Acceptance

- [ ] `.agent/skills/` 目录已创建
- [ ] `.flow/bin/flowctl` 已复制且可执行
- [ ] `.gitignore` 包含 rp-cli 输出、临时文件排除规则
- [ ] 从 repo 内任意 cwd 可成功执行 flowctl:
  ```bash
  REPO_ROOT="$(git rev-parse --show-toplevel)"
  cd "$REPO_ROOT/some/subdir" && "$REPO_ROOT/.flow/bin/flowctl" list
  ```

## Done summary

TBD

## Evidence

- Commits:
- Tests:
- PRs:
