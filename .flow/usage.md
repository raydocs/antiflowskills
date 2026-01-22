# Flow-Next Usage Guide

Task tracking for AI agents.

**State locations:**
- **Spec state** (versioned): Epic/task specs in `.flow/` - tracked by git
- **Runtime state** (unversioned): Status, assignee in `.git/flow-state/` - shared across worktrees

## CLI

Use dynamic repo root for commands that may run from subdirectories or worktrees:

```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"
"$REPO_ROOT/.flow/bin/flowctl" --help              # All commands
"$REPO_ROOT/.flow/bin/flowctl" <cmd> --help        # Command help
```

## File Structure

```
.flow/
├── bin/flowctl         # CLI (this install)
├── epics/fn-N-xxx.json # Epic metadata
├── specs/fn-N-xxx.md   # Epic specifications
├── tasks/fn-N-xxx.M.json # Task metadata
├── tasks/fn-N-xxx.M.md   # Task specifications
├── memory/             # Context memory
└── meta.json           # Project metadata
```

## IDs

- Epics: `fn-N-xxx` (e.g., fn-1-abc, fn-2-z9k) or legacy `fn-N`
- Tasks: `fn-N-xxx.M` (e.g., fn-1-abc.1, fn-1-abc.2) or legacy `fn-N.M`

## Common Commands

```bash
# Setup (run once per shell session)
REPO_ROOT="$(git rev-parse --show-toplevel)"
FLOWCTL="$REPO_ROOT/.flow/bin/flowctl"

# List
$FLOWCTL list                    # All epics + tasks grouped
$FLOWCTL epics                   # All epics with progress
$FLOWCTL tasks                   # All tasks
$FLOWCTL tasks --epic fn-1-abc   # Tasks for epic (new ID format)
$FLOWCTL tasks --epic fn-1       # Tasks for epic (legacy format)
$FLOWCTL tasks --status todo     # Filter by status

# View
$FLOWCTL show fn-1-abc           # Epic with all tasks
$FLOWCTL show fn-1-abc.2         # Single task
$FLOWCTL cat fn-1-abc            # Epic spec (markdown)
$FLOWCTL cat fn-1-abc.2          # Task spec (markdown)

# Status
$FLOWCTL ready --epic fn-1-abc   # What's ready to work on
$FLOWCTL validate --all          # Check structure
$FLOWCTL state-path              # Show state directory (for worktrees)

# Create
$FLOWCTL epic create --title "..."
$FLOWCTL task create --epic fn-1-abc --title "..."

# Work
$FLOWCTL start fn-1-abc.2        # Claim task
$FLOWCTL done fn-1-abc.2 --summary-file s.md --evidence-json e.json
```

## Workflow

1. `$FLOWCTL epics` - list all epics
2. `$FLOWCTL ready --epic fn-1-abc` - find available tasks
3. `$FLOWCTL start fn-1-abc.2` - claim task
4. Implement the task
5. `$FLOWCTL done fn-1-abc.2 --summary-file ... --evidence-json ...` - complete

## Evidence JSON Format

```json
{"commits": ["abc123"], "tests": ["npm test"], "prs": []}
```

## Parallel Worktrees

Runtime state (status, assignee, etc.) is stored in `.git/flow-state/`, shared across worktrees:

```bash
$FLOWCTL state-path              # Show state directory
$FLOWCTL migrate-state           # Migrate existing repo
$FLOWCTL migrate-state --clean   # Migrate + remove runtime from tracked files
```

Migration is optional - existing repos work without changes.

### Worktree Usage

Git worktrees allow parallel work on different branches. Flow-Next state is shared across worktrees via `.git/flow-state/`:

```bash
# Create worktree for a feature branch
git worktree add ../my-feature-worktree feature-branch

# In the worktree, flowctl works the same way
cd ../my-feature-worktree
REPO_ROOT="$(git rev-parse --show-toplevel)"
"$REPO_ROOT/.flow/bin/flowctl" list

# State is shared - task claimed in main worktree is visible here
```

**Key points:**
- All worktrees share the same `.git/flow-state/` directory
- Task assignments and status are visible across all worktrees
- Spec files (`.flow/`) are part of the repo and follow normal git branching

## Factory/Droid Skills

Factory/Droid skills are located in `.factory/skills/`. See `docs/factory-skills-guide.md` for complete usage.

**Key differences from Claude Code:**
- Skills are model-invoked (semantic triggering) not slash commands
- Ralph autonomous mode is not available (requires hooks system)
- Hooks system is not supported

**RepoPrompt (rp-cli) limitation:**
- rp-cli is macOS only
- Non-macOS users: Use `repo-scout` for codebase exploration
- Review skills (`flow-impl-review`, `flow-plan-review`) require rp-cli or manual review

## More Info

- Human docs: https://github.com/gmickel/gmickel-claude-marketplace/blob/main/plugins/flow-next/docs/flowctl.md
- CLI reference: `$FLOWCTL --help`
- Factory skills guide: `docs/factory-skills-guide.md`
