# Factory Environment Verification Report

**Task:** fn-2-l67.1
**Date:** 2026-01-22
**Status:** Complete

## 1. Directory Structure Verification

### Skills Directory
Factory recognizes skills from two locations:
- **Workspace (shared):** `<repo>/.factory/skills/` - checked into git, shared with team
- **Personal (private):** `~/.factory/skills/` - machine-specific, not shared

Each skill must be in its own directory: `.factory/skills/<skill-name>/SKILL.md`

**Confirmed:** Factory uses `.factory/skills/` (NOT `.agent/skills/`)

## 2. Working Directory (cwd) Rules

### During Skill Execution
- Factory CLI supports `--cwd <path>` flag for specifying execution location
- Skills execute in the directory specified by `--cwd` or current working directory
- **Rule:** Skills should NOT assume a fixed cwd; use `git rev-parse --show-toplevel` to locate repo root

### Recommended Pattern
```bash
REPO_ROOT="${REPO_ROOT:-$(git rev-parse --show-toplevel 2>/dev/null || true)}"
if [ -z "$REPO_ROOT" ]; then
  echo "Error: Set REPO_ROOT=/absolute/path/to/repo (git rev-parse failed)"
  exit 1
fi
```

## 3. Tool Mapping Table (Claude -> Factory)

| Claude Tool | Factory Equivalent | Exists? | Degradation Strategy |
|-------------|-------------------|---------|---------------------|
| **Grep** | `Grep` | YES | Native support, no degradation needed |
| **Glob** | `Glob` | YES | Native support, no degradation needed |
| **Read** | `Read` | YES | Native support, no degradation needed |
| **Task** (subagent) | `Task` | YES | Native support via Custom Droids |
| **Bash** | `Execute` | YES | Native support, no degradation needed |
| **WebSearch** | `WebSearch` | YES | In `web` tool category |
| **WebFetch** | `FetchUrl` | YES | In `web` tool category |
| **Edit** | `Edit` | YES | In `edit` tool category |
| **Write** | `Create` | YES | In `edit` tool category |
| **ApplyPatch** | `ApplyPatch` | YES | In `edit` tool category |
| **LS** | `LS` | YES | In `read-only` tool category |
| **git CLI** | git CLI | YES | Via Execute tool |
| **gh CLI** | gh CLI | YES | Via Execute tool (if installed) |
| **rp-cli** | rp-cli | PARTIAL | macOS only; degrade to repo-scout |
| **TodoWrite** | `TodoWrite` | YES | Auto-included for all droids |

### Factory Tool Categories
```yaml
read-only: [Read, LS, Grep, Glob]
edit: [Create, Edit, ApplyPatch]
execute: [Execute]
web: [WebSearch, FetchUrl]
mcp: [Dynamically populated]
```

### Key Finding
**Factory has near-complete tool parity with Claude Code.** Most tools map directly. The Task tool for subagent invocation is natively supported.

## 4. SKILL.md Format Requirements

### YAML Frontmatter (Required Fields)

```yaml
---
name: skill-name
description: Brief explanation of what problem it solves and when to use it
---
```

| Field | Required? | Notes |
|-------|-----------|-------|
| `name` | YES | Lowercase, hyphenated identifier |
| `description` | YES | Up to 500 chars; used for semantic triggering |
| `model` | NO | `inherit` (default) or specific model ID |
| `reasoningEffort` | NO | `low`, `medium`, or `high` |
| `tools` | NO | Omit for all, or specify array/category |

### Name Matching
- Directory name SHOULD match the `name` field in frontmatter
- Example: `.factory/skills/flow-next-plan/SKILL.md` with `name: flow-next-plan`

## 5. Verification Section Requirements

### Format
Every SKILL.md MUST include a `## Verification` section with executable bash commands.

```markdown
## Verification

```bash
REPO_ROOT="${REPO_ROOT:-$(git rev-parse --show-toplevel 2>/dev/null || true)}"
if [ -z "$REPO_ROOT" ]; then
  echo "Error: Set REPO_ROOT=/absolute/path/to/repo"
  exit 1
fi

# Verify skill exists
ls "$REPO_ROOT/.factory/skills/<skill-name>/SKILL.md"
# Expected: File exists, exit code 0

# Verify dependency
which flowctl || ls "$REPO_ROOT/.flow/bin/flowctl"
# Expected: flowctl found, exit code 0
```
```

### Requirements
- Each command should have `# Expected:` comment explaining success condition
- Include fallback procedures when verification fails
- Use 4-backtick fence to avoid nesting issues

## 6. Skill Naming Conventions

| Convention | Example | Notes |
|------------|---------|-------|
| Lowercase | `flow-next-plan` | NOT `FlowNextPlan` |
| Hyphenated | `impl-review` | NOT `impl_review` |
| Descriptive | `repo-scout` | Purpose clear from name |
| Directory = name | `.factory/skills/repo-scout/` | Match frontmatter `name` |

## 7. Skill Discovery and Loading

### Discovery
- Skills are automatically scanned from `.factory/skills/` and `~/.factory/skills/`
- No explicit activation required
- Project skills override personal skills with same name

### Verification Method
```bash
# List available tools (includes skills)
droid exec --list-tools

# Interactive check
droid
# Then observe skill availability in session
```

## 8. Auxiliary Files Support

| File | Status | Purpose |
|------|--------|---------|
| `references.md` | SUPPORTED | Pointers to types, APIs, modules |
| `schemas/` | SUPPORTED | JSON/YAML schemas, OpenAPI snippets |
| `checklists.md` | SUPPORTED | Validation/rollout checklists |
| `workflow.md` | SUPPORTED | Co-located supporting files |
| `steps.md` | SUPPORTED | Step-by-step procedures |
| `phases.md` | SUPPORTED | Phase definitions |
| Other `.md` files | SUPPORTED | Any relative path references |

**Key:** Factory explicitly supports co-located supporting files that provide structured context.

## 9. Skill Inter-Invocation (Subagent)

### Mechanism
Skills invoke other skills via the **Task tool**. Factory's Custom Droids system handles subagent delegation.

### How It Works
1. Primary droid identifies need for specialized capability
2. Invokes Task tool with droid name: "Use the subagent `repo-scout` to analyze this codebase"
3. Subagent executes with fresh context window
4. Results stream back in real-time

### Configuration
Custom droids are defined in `.factory/droids/<name>.md` with:
```yaml
---
name: droid-name
description: Specialized purpose
model: inherit  # or specific model
tools: [read-only, execute]  # or omit for all
---
```

### Key Difference from Claude Code
- Claude Code: `Task` tool creates subagent with same capabilities
- Factory: Custom Droids have explicit tool restrictions via `tools` field

## 10. Model-Invoked vs Slash Commands

### Key Distinction
| Type | Trigger | Example |
|------|---------|---------|
| **Model-invoked** (Skills) | Semantic matching | "Plan a feature implementation" |
| **User-invoked** (Slash Commands) | Explicit command | `/review-pr 123` |

### Description Writing for Semantic Triggering

**DON'T:**
```yaml
description: "Triggers on /flow-next:plan or when user says 'plan'"
```

**DO:**
```yaml
description: "Plan and design feature implementations with structured task breakdown. Use when the user wants to architect a new feature, create an implementation roadmap, or break down a complex requirement into actionable tasks."
```

## Summary

Factory/Droid provides excellent compatibility with Claude Code skills:
- **Directory:** `.factory/skills/` confirmed
- **Tools:** Near-complete parity (Grep, Glob, Read, Edit, Execute, Task all supported)
- **Subagents:** Native Task tool support with Custom Droids
- **Auxiliary files:** Fully supported
- **Key requirement:** Verification sections mandatory

### Migration Checklist
- [x] Confirm `.factory/skills/` directory recognition
- [x] Document cwd rules (use `git rev-parse --show-toplevel`)
- [x] Complete tool mapping table
- [x] Document Verification section format
- [x] Document YAML frontmatter requirements
- [x] Document skill naming conventions
- [x] Document skill discovery/loading
- [x] Document auxiliary file support
- [x] Document subagent invocation mechanism
