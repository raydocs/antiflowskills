# Factory/Droid Skills Guide

Complete guide for using Flow-Next skills with Factory/Droid AI agents.

## Overview

Factory/Droid skills are located in `.factory/skills/`. Unlike Claude Code slash commands, Factory skills are **model-invoked** (semantic triggering) - the model selects skills based on natural language context rather than explicit `/command` syntax.

## Path Resolution

All skills use dynamic repo root resolution. The pattern used in skill implementations:

```bash
REPO_ROOT="${REPO_ROOT:-$(git rev-parse --show-toplevel 2>/dev/null || true)}"
if [ -z "$REPO_ROOT" ]; then
  echo "Error: Set REPO_ROOT=/absolute/path/to/repo (git rev-parse failed; cwd may be outside repo)."
  exit 1
fi
FLOWCTL="$REPO_ROOT/.flow/bin/flowctl"
```

For human/agent use, the simplified form suffices (assumes cwd is within repo):

```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"
"$REPO_ROOT/.flow/bin/flowctl" <command>
```

## Available Skills

### Core Skills

| Skill | Description | Trigger Examples |
|-------|-------------|------------------|
| `flow-next` | Task management operations | "show my tasks", "list epics", "add a task", "what's ready" |
| `flow-plan` | Plan features with structured task breakdown | "plan a feature for...", "design implementation for..." |
| `flow-work` | Execute tasks with git workflow | "implement fn-1-abc", "work on the plan", "execute the tasks" |
| `flow-interview` | In-depth requirements gathering | "interview me about...", "refine requirements for..." |

### Review Skills

| Skill | Description | Trigger Examples |
|-------|-------------|------------------|
| `flow-plan-review` | Carmack-level plan review | "review the plan", "check the epic spec" |
| `flow-impl-review` | Carmack-level code review | "review the implementation", "check the code changes" |

**Note:** Review skills require rp-cli (macOS only). See [Platform Limitations](#platform-limitations).

### Scout Skills (Research Subagents)

| Skill | Description | Trigger Examples |
|-------|-------------|------------------|
| `repo-scout` | Find patterns and conventions in codebase | "find existing patterns for...", "what conventions does this repo use" |
| `context-scout` | Token-efficient deep codebase exploration | "explore the codebase for...", "understand how X works" |
| `docs-scout` | Find framework/library documentation | "find docs for...", "what does the API look like" |
| `practice-scout` | Find industry best practices | "what are best practices for...", "how should I implement X" |
| `github-scout` | Search GitHub for code/issues | "find examples on GitHub", "search for similar implementations" |
| `docs-gap-scout` | Identify documentation gaps | "what documentation is missing" |
| `epic-scout` | Analyze epic requirements | "analyze the epic requirements" |
| `flow-gap-analyst` | Find gaps in plans | "what's missing from the plan" |
| `memory-scout` | Search project memory | "what decisions were made about..." |

## Semantic Triggering

Factory skills are triggered by natural language matching rather than slash commands. The model analyzes user requests and selects appropriate skills.

### Examples

| User Request | Skill Triggered |
|--------------|-----------------|
| "Plan: Add OAuth login for users" | `flow-plan` |
| "What tasks do I have?" | `flow-next` |
| "Review the code changes" | `flow-impl-review` |
| "Find patterns for validation in this repo" | `repo-scout` |
| "How does React 19 handle suspense?" | `docs-scout` |

### Tips for Clear Triggering

- Be explicit about intent: "plan" vs "implement" vs "review"
- Include Flow IDs when referring to existing tasks: "work on fn-1-abc.2"
- For scouts, specify the domain: "find codebase patterns" vs "find documentation"

## Verification Commands

Each skill includes a Verification section. Run these to confirm skill installation:

```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"

# Verify all skills exist
ls "$REPO_ROOT/.factory/skills/"

# Verify specific skill
ls "$REPO_ROOT/.factory/skills/flow-next/SKILL.md"

# Verify flowctl works
"$REPO_ROOT/.flow/bin/flowctl" list
```

### Quick Health Check

```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"

# Check skill count
SKILL_COUNT=$(ls -d "$REPO_ROOT/.factory/skills/"*/ 2>/dev/null | wc -l | tr -d ' ')
printf 'Skills: %s\n' "$SKILL_COUNT"

# Check flowctl
"$REPO_ROOT/.flow/bin/flowctl" --version 2>/dev/null && echo "flowctl: OK" || echo "flowctl: ERROR"

# Check rp-cli (macOS only)
if [ "$(uname)" = "Darwin" ]; then
  rp-cli --version 2>/dev/null && echo "rp-cli: OK" || echo "rp-cli: not installed"
else
  echo "rp-cli: not available (non-macOS)"
fi
```

## Platform Limitations

### rp-cli (macOS Only)

RepoPrompt's `rp-cli` is only available on macOS. This affects:

| Feature | macOS | Non-macOS |
|---------|-------|-----------|
| `context-scout` | Full functionality | Degrades to `repo-scout` (manual grep/find exploration) |
| `flow-impl-review` | Full functionality | Manual review required |
| `flow-plan-review` | Full functionality | Manual review required |

**Non-macOS Workarounds:**

1. **For codebase exploration:** Use `repo-scout` instead of `context-scout`. This provides ripgrep-based exploration instead of RepoPrompt context packaging.
2. **For code review:** Perform manual review or use external tools.
3. **To skip review:** Pass `--review=none` to skill invocation (e.g., in `flow-work` or `flow-impl-review` setup questions).
4. **Suggested message:** "rp-cli is macOS only. Use repo-scout for codebase exploration, or review code manually."

### Hooks System (Not Available)

Factory does not support the Claude Code hooks system. This means:

- **Ralph autonomous mode:** Not available
- **Pre/post task hooks:** Not available
- **Custom automation:** Must be triggered manually

## Differences from Claude Code

| Feature | Claude Code | Factory/Droid |
|---------|-------------|---------------|
| Trigger style | Slash commands (`/flow-next:plan`) | Semantic (model-invoked) |
| Skills location | Plugin marketplace | `.factory/skills/` |
| Hooks system | Supported | Not available |
| Ralph mode | Supported | Not available |
| rp-cli | macOS only | macOS only |
| flowctl | Shared | Shared |

## Workflow Examples

### Planning a Feature

1. User: "Plan a feature for user authentication with OAuth"
2. Model invokes `flow-plan` skill
3. Skill asks clarifying questions (depth, review preference)
4. Model creates epic with tasks in `.flow/`

### Working Through Tasks

1. User: "Work on fn-1-abc"
2. Model invokes `flow-work` skill
3. Skill asks setup questions (branch, review preference)
4. Model implements tasks in order, running tests, committing

### Reviewing Implementation

1. User: "Review the implementation"
2. Model invokes `flow-impl-review` skill
3. If rp-cli available: Sends to RepoPrompt for review
4. If not: Provides degradation message

## Troubleshooting

### "git rev-parse failed" Error

**Cause:** Command run from outside the git repository.

**Fix:** Either:
- Run from within the repository
- Set `REPO_ROOT` environment variable: `export REPO_ROOT=/path/to/repo`

### "flowctl not found" Error

**Cause:** `.flow/bin/flowctl` not installed.

**Fix:** Ensure the repository has the Flow-Next setup. Check:
```bash
ls -la "$REPO_ROOT/.flow/bin/flowctl"
```

### Skills Not Triggering

**Cause:** Model not matching semantic triggers.

**Fix:** Be more explicit:
- Instead of "help with this" use "plan this feature" or "implement fn-1-abc.2"
- Use skill names directly if needed: "use the flow-plan skill to..."

### Review Skills Not Working (Non-macOS)

**Cause:** rp-cli is macOS only.

**Fix:**
- Use `--review=none` flag when invoking review skills (`flow-impl-review`, `flow-plan-review`) to skip external review
- When using `flow-work`, answer "none" to the review setup question
- Perform manual code review
- Or run review from a macOS machine with rp-cli installed

## File Structure

```
.factory/
  skills/
    flow-next/
      SKILL.md           # Task management
    flow-plan/
      SKILL.md           # Planning
      steps.md           # Planning workflow
    flow-work/
      SKILL.md           # Execution
      phases.md          # Execution phases
    flow-interview/
      SKILL.md           # Requirements gathering
    flow-impl-review/
      SKILL.md           # Code review
      workflow.md        # Review workflow
    flow-plan-review/
      SKILL.md           # Plan review
      workflow.md        # Review workflow
    repo-scout/
      SKILL.md           # Codebase patterns
    context-scout/
      SKILL.md           # Deep exploration
    docs-scout/
      SKILL.md           # Documentation
    practice-scout/
      SKILL.md           # Best practices
    github-scout/
      SKILL.md           # GitHub search
    ... (additional scouts)
```

## Quick Reference

```bash
# Setup
REPO_ROOT="$(git rev-parse --show-toplevel)"
FLOWCTL="$REPO_ROOT/.flow/bin/flowctl"

# List tasks
$FLOWCTL list

# View task
$FLOWCTL show fn-1-abc.2
$FLOWCTL cat fn-1-abc.2

# Start task
$FLOWCTL start fn-1-abc.2

# Complete task
$FLOWCTL done fn-1-abc.2 --summary-file /tmp/summary.md --evidence-json /tmp/evidence.json

# Verify skills
ls "$REPO_ROOT/.factory/skills/"
```
