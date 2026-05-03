# tdd-guardian

TDD Guardian plugin for Claude Code, OpenCode, and Codex CLI. Enforces strict test-driven development with automated quality gates.

## Cross-platform support

This plugin works across multiple AI coding assistants:

| Platform | Plugin manifest | Config path | Skill discovery |
|----------|----------------|-------------|-----------------|
| **Claude Code** | `.claude-plugin/plugin.json` | `.claude/tdd-guardian/config.json` | `.claude-plugin/` hooks + `skills/` |
| **Codex CLI** | `.codex-plugin/plugin.json` | `.opencode/tdd-guardian/config.json` | `skills/*/SKILL.md` |
| **OpenCode** | (uses skill standard) | `.opencode/tdd-guardian/config.json` | `skills/*/SKILL.md` |

Hook scripts search both `.claude/tdd-guardian/` and `.opencode/tdd-guardian/` for config, using whichever exists.

## Project structure

```
.claude-plugin/
  plugin.json             Claude Code plugin metadata
.codex-plugin/
  plugin.json             Codex CLI plugin metadata
hooks/
  hooks.json              Hook registration (Claude Code auto-discovery)
agents/                   Specialized subagents for TDD workflow (Claude Code)
  tdd-planner.md          Work item planning
  tdd-test-designer.md    Behavior-driven test design
  tdd-implementer.md      Small-batch implementation
  tdd-coverage-auditor.md Coverage gate enforcement
  tdd-mutation-auditor.md Mutation testing
  tdd-reviewer.md         Final code + test quality review
commands/                 Slash command definitions (Claude Code)
  tdd-guardian-init.md    /init — initialize config
  tdd-guardian-workflow.md /workflow — full TDD orchestration
config/
  config.json             Default configuration template
scripts/
  tdd-guardian/
    pretool_guard.js      PreToolUse hook — blocks commits without fresh gates
    taskcompleted_gate.js TaskCompleted hook — runs gates on task completion
skills/                   Cross-platform skills (SKILL.md format)
  tdd-guardian/
    init/                 Workspace initialization
    workflow/             TDD workflow orchestration
    policy-core/          Global TDD governance policy
    test-matrix/          Test matrix design
    coverage-gate/        Coverage enforcement
    mutation-gate/        Mutation testing
    review-gate/          Code + test quality review
```

## Conventions

### Test quality enforcement

All tests must have at least one Level 1-5 (behavior) assertion. Tests with only Level 6-7 (wiring) assertions are rejected. See `skills/tdd-guardian/policy-core/SKILL.md` for the full assertion hierarchy.

### Hook scripts

- Hooks are registered via `hooks/hooks.json` using `${CLAUDE_PLUGIN_ROOT}` paths (Claude Code)
- `pretool_guard.js` intercepts Bash tool calls matching commit/push/publish patterns
- `taskcompleted_gate.js` runs test/coverage/mutation gates on task completion
- Both search for config in `.claude/tdd-guardian/` first, then `.opencode/tdd-guardian/`
- Gate freshness state is written alongside the config file as `state.json`

### Adding new skills

1. Create `skills/tdd-guardian/<name>/SKILL.md` with YAML frontmatter
2. Reference `policy-core` skill for governance rules
3. Update `README.md`

### Adding new agents

1. Create `agents/tdd-<name>.md` with YAML frontmatter
2. List required tools and skills in frontmatter
3. Reference in `commands/tdd-guardian-workflow.md` if part of the workflow
4. Update `README.md`
