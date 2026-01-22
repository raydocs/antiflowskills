<!-- BEGIN FLOW-NEXT -->
## Flow-Next

This project uses Flow-Next for task tracking. Use `.flow/bin/flowctl` instead of markdown TODOs or TodoWrite.

**Quick commands:**
```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"
"$REPO_ROOT/.flow/bin/flowctl" list                # List all epics + tasks
"$REPO_ROOT/.flow/bin/flowctl" epics               # List all epics
"$REPO_ROOT/.flow/bin/flowctl" tasks --epic fn-N   # List tasks for epic
"$REPO_ROOT/.flow/bin/flowctl" ready --epic fn-N   # What's ready
"$REPO_ROOT/.flow/bin/flowctl" show fn-N.M         # View task
"$REPO_ROOT/.flow/bin/flowctl" start fn-N.M        # Claim task
"$REPO_ROOT/.flow/bin/flowctl" done fn-N.M --summary-file s.md --evidence-json e.json
```

**Rules:**
- Use `.flow/bin/flowctl` for ALL task tracking
- Do NOT create markdown TODOs or use TodoWrite
- Re-anchor (re-read spec + status) before every task

**More info:** `.flow/bin/flowctl --help` or read `.flow/usage.md`

## Platform-Specific Skills

This project supports three AI agent platforms:

| Platform | Skills Location | Trigger Style |
|----------|-----------------|---------------|
| Claude Code | Plugin marketplace | Slash commands (`/flow-next:plan`) |
| Antigravity | `.agent/skills/` | Slash commands |
| Factory/Droid | `.factory/skills/` | Semantic (model-invoked) |

**Factory/Droid skills** are triggered by natural language. For example:
- "Plan a feature for user authentication" triggers `flow-plan`
- "Review the implementation" triggers `flow-impl-review`
- "Find patterns in the codebase" triggers `repo-scout`

**RepoPrompt integration (rp-cli):**
- macOS only - required for `context-scout`, `flow-impl-review`, `flow-plan-review`
- Non-macOS: Use `repo-scout` for codebase exploration, skip external review

**See:** `docs/factory-skills-guide.md` for complete Factory/Droid usage guide.
<!-- END FLOW-NEXT -->
