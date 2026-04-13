---
name: init
description: Initialize workspace TDD Guardian config and enable strict hooks for test/coverage enforcement.
---

# Initialize TDD Guardian

Create TDD Guardian config using project-appropriate commands.

Config is written to `.claude/tdd-guardian/config.json` (Claude Code) or `.opencode/tdd-guardian/config.json` (OpenCode/Codex CLI). Detect the active agent and write to the appropriate path.

## Steps

1. Detect package manager and test stack from project files.
2. Propose commands for:
   - `testCommand`
   - `coverageCommand`
   - `mutationCommand` (optional)
3. **Ask the user** whether to enable blocking hooks:
   - `enforceOnTaskCompleted` — run gates automatically on task completion
   - `blockCommitWithoutFreshGate` — block commit/push/publish without fresh gates
   - Default: both `false` (non-blocking). Skills, agents, and slash commands still work — only automatic blocking is opt-in.
4. Write config file.

Default config template:

```json
{
  "enabled": true,
  "enforceOnTaskCompleted": false,
  "blockCommitWithoutFreshGate": false,
  "gateFreshnessMinutes": 120,
  "bypassEnv": "TDD_GUARD_BYPASS",
  "preflightCommand": "",
  "testCommand": "pnpm test",
  "coverageCommand": "pnpm test -- --coverage",
  "coverageSummaryPath": "coverage/coverage-summary.json",
  "coverageThresholds": {
    "lines": 100,
    "functions": 100,
    "branches": 100,
    "statements": 100
  },
  "coverageMode": "absolute",
  "smartStaleness": true,
  "requireMutation": false,
  "mutationCommand": ""
}
```

### Coverage mode

- `"absolute"` (default): must meet configured thresholds
- `"no-decrease"`: blocks only if coverage decreased from recorded baseline (useful for projects with pre-existing coverage gaps)

### Smart staleness

When `smartStaleness: true` (default), stale gate timestamps are allowed if no source files have changed since the last gate pass. This prevents unnecessary re-runs when stepping away from the project.

If project is not Node-based, replace commands with project-native equivalents.
