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

## Factory/Droid Skills

Factory/Droid skills are located in `.factory/skills/`. Skills are model-invoked (semantic triggering) rather than slash commands.

**Available skill categories:**
- **Core**: `flow-next`, `flow-plan`, `flow-work`, `flow-interview`
- **Review**: `flow-plan-review`, `flow-impl-review`
- **Scouts**: `repo-scout`, `context-scout`, `docs-scout`, `practice-scout`, `github-scout`, and more

**RepoPrompt integration** (rp-cli): macOS only. Non-macOS users should use `repo-scout` or manual commands as fallback.

**See:** `docs/factory-skills-guide.md` for complete usage guide.
<!-- END FLOW-NEXT -->
