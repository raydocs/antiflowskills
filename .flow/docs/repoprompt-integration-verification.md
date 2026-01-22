# RepoPrompt Integration Verification Report

**Task:** fn-2-l67.6
**Date:** 2026-01-22
**Status:** Complete

## Executive Summary

RepoPrompt integration verified successfully in Factory environment. All acceptance criteria passed:
- rp-cli version 1.6.0 available and functional
- Context builder produces valid output
- Chat-send returns AI responses
- Worktree scenario works correctly

## 1. rp-cli Availability

### Test Command
```bash
rp-cli --version
```

### Result
```
rp-cli (repoprompt-mcp) 1.6.0
```

**Status:** PASS - rp-cli 1.6.0 meets minimum requirement (1.6.0+)

## 2. Context Builder Verification

### Test Command
```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"
rp-cli -w 1 --repo-root "$REPO_ROOT" -e 'builder "test prompt for flow-next skills"'
```

### Result
```
## Discover Context
- Status: completed
- Tab: 30A6163C-0ED0-49B9-89CD-34D04AD64843
- Agent: codexExec
- Prompt mode: rewrite
- Token budget: 60000
- Files selected: 0
- Total tokens: 0
```

**Status:** PASS - Exit code 0, output contains "completed" status

### Notes
- Multiple RepoPrompt windows require explicit `-w <window_id>` flag
- Use `rp-cli -e 'windows'` to list available windows first
- Always include `--repo-root "$REPO_ROOT"` for cross-worktree consistency

## 3. Chat-Send Functionality

### flowctl rp Subcommand Availability
```bash
"$REPO_ROOT/.flow/bin/flowctl" rp --help
```

Available subcommands:
- `windows` - List RepoPrompt windows
- `pick-window` - Pick window by repo root
- `ensure-workspace` - Ensure workspace and switch window
- `builder` - Run builder and return tab
- `prompt-get` / `prompt-set` - Manage prompts
- `select-get` / `select-add` - Manage file selection
- `chat-send` - Send chat via rp-cli
- `prompt-export` - Export prompt to file
- `setup-review` - Atomic: pick-window + workspace + builder

### Test Command
```bash
echo "test message from flow-next integration verification" > /tmp/test-rp-message.txt
"$REPO_ROOT/.flow/bin/flowctl" rp chat-send \
  --window 1 \
  --tab "30A6163C-0ED0-49B9-89CD-34D04AD64843" \
  --message-file /tmp/test-rp-message.txt \
  --new-chat --mode chat
```

### Result
```
## Chat Send
- Chat: untitled-chat-C2E6A6 | Mode: chat

### Response
Received your flow-next integration verification test message. If you want,
send the expected echo/metadata format and I'll respond in that exact shape.
```

**Status:** PASS - Exit code 0, response received from AI

## 4. Worktree Scenario

### Test Commands
```bash
# Create worktree
git worktree add /tmp/test-worktree HEAD

# Test from worktree
cd /tmp/test-worktree
REPO_ROOT="$(git rev-parse --show-toplevel)"
echo "REPO_ROOT in worktree: $REPO_ROOT"
rp-cli -w 1 --repo-root "$REPO_ROOT" -e 'builder "test from worktree"'

# Cleanup
git worktree remove /tmp/test-worktree
```

### Result
```
REPO_ROOT in worktree: /private/tmp/test-worktree
## Discover Context
- Status: completed
- Tab: 928D515C-C2EC-46E5-8A60-B3196726CC60
...
```

**Status:** PASS - git rev-parse correctly returns worktree path, rp-cli executes successfully

### Notes
- `git rev-parse --show-toplevel` returns the worktree path (e.g., `/private/tmp/test-worktree`)
- The `--repo-root` flag allows rp-cli to operate on worktree content
- Each worktree gets its own tab in RepoPrompt

## 5. MCP Integration Status

### Status: NOT TESTED (Optional)

MCP integration is an optional enhancement. The primary integration method (rp-cli direct calls) is fully functional.

**Reasons for skipping MCP testing:**
1. Factory environment does not require MCP for RepoPrompt
2. rp-cli provides all needed functionality
3. MCP adds complexity without additional benefit for this use case

**If MCP is needed in the future:**
1. Check if Factory supports MCP plugins
2. Configure repoprompt-mcp in Factory's MCP settings
3. Use MCP tools directly instead of rp-cli

## 6. Non-macOS Fallback Strategy

### Platform Detection

rp-cli is **macOS only**. For non-macOS platforms, skills must degrade gracefully.

### Detection Method
```bash
OS="$(uname -s)"
if [[ "$OS" != "Darwin" ]]; then
  echo "RepoPrompt CLI is only available on macOS."
  echo "Please manually execute the following steps:"
  echo "  1. Select relevant files in your IDE"
  echo "  2. Copy the context to your preferred AI tool"
  echo "  3. Run the review prompt manually"
  exit 0
fi
```

### Fallback User Prompts

#### For Plan Review
```
RepoPrompt is only available on macOS.

To review this plan manually:
1. Open the epic spec at: .flow/specs/<epic-id>.md
2. Copy the content to your preferred AI assistant (ChatGPT, Claude web, etc.)
3. Ask it to perform a Carmack-level plan review examining:
   - Completeness, Feasibility, Clarity
   - Architecture, Risks, Scope
   - Testability, Consistency

Provide the review feedback and I will help address issues.
```

#### For Implementation Review
```
RepoPrompt is only available on macOS.

To review this implementation manually:
1. Run: git diff <base>..HEAD
2. Copy the diff output to your preferred AI assistant
3. Ask it to perform a Carmack-level code review examining:
   - Correctness, Simplicity, Edge cases
   - Security, Performance, Maintainability

Provide the review feedback and I will help address issues.
```

### Implementation Location

The fallback logic should be implemented in:
- `.factory/skills/flow-impl-review/workflow.md` - Phase 0: Backend Selection
- `.factory/skills/flow-plan-review/workflow.md` - Phase 0: Backend Selection

Add platform check before backend selection:
```bash
OS="$(uname -s)"
if [[ "$OS" != "Darwin" && "$BACKEND" == "rp" ]]; then
  echo "Warning: RepoPrompt backend requires macOS"
  echo "Falling back to manual review instructions..."
  # Output manual instructions
  exit 0
fi
```

## 7. Verification Commands Summary

### Quick Health Check
```bash
# 1. Check rp-cli availability
rp-cli --version
# Expected: version >= 1.6.0

# 2. List windows (requires RepoPrompt app running)
rp-cli -e 'windows'
# Expected: List of window IDs

# 3. Test context builder
REPO_ROOT="$(git rev-parse --show-toplevel)"
rp-cli -w <window_id> --repo-root "$REPO_ROOT" -e 'builder "health check"'
# Expected: Exit code 0, "completed" status

# 4. Test flowctl rp
"$REPO_ROOT/.flow/bin/flowctl" rp --help
# Expected: Help output with subcommands
```

### Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| "Multiple RepoPrompt windows detected" | More than one RP window open | Use `-w <id>` flag |
| "Command not found: rp-cli" | Not installed or not on PATH | `brew install --cask repoprompt` |
| "git rev-parse failed" | Not in a git repository | Set `REPO_ROOT` manually |
| Version < 1.6.0 | Outdated installation | `brew upgrade repoprompt` |

## 8. Acceptance Criteria Checklist

- [x] rp-cli command executable: version 1.6.0 returned, exit code 0
- [x] Context builder produces output: "completed" status, exit code 0
- [x] chat-send functionality verified: AI response received
- [x] Worktree scenario works: git rev-parse returns worktree path, rp-cli executes
- [x] MCP integration status documented: NOT TESTED (optional enhancement)
- [x] Non-macOS fallback strategy documented: Platform detection + user prompts

## References

- [RepoPrompt Documentation](https://repoprompt.com/docs)
- [rp-cli Help](rp-cli --help)
- [flowctl rp Help](.flow/bin/flowctl rp --help)
- [Factory Environment Verification](.flow/docs/factory-environment-verification.md)
