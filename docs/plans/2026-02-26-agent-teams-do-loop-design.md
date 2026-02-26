# Agent Teams Do-Loop Design

## Summary

Introduce Claude Code Agent Teams into bugfix and feature-development plugins using a shared "do-loop" pattern. The main session acts as team lead, spawning doer+critic teammate pairs that iterate until the critic approves (max 3 rounds). A shared skill defines the iteration protocol. No hooks for now.

## Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Team lead | User's main session | Simplest, full visibility, user can interact with any teammate |
| Loop enforcement | Skill only (no hooks) | Hooks can't run project-agnostic test commands; add hooks later when API matures |
| Integration with existing pipeline | Replace entirely | No dual codepath; all-in on teams |
| Pattern | Shared skill + per-plugin commands | Skill defines reusable protocol, commands define team composition |
| Feature-dev implementation phase | 2 teammates (implementer + reviewer) | Tests as separate Phase 4; keeps each loop focused |

## New Plugin: shared

Single skill, no agents, no commands, no hooks.

```
plugins/shared/
├── .claude-plugin/plugin.json
└── skills/
    └── do-loop.md
```

### do-loop skill contents

1. **Team spawning protocol** — how the lead describes teammates (role, model, context to pass)
2. **Task structure** — tasks with clear acceptance criteria
3. **Iteration rules** — max 3 rounds per loop; after round 3 escalate to user
4. **Communication protocol** — reviewer messages doer with specific issues (file:line + what's wrong + fix). Doer messages reviewer when ready for re-review. No broadcast.
5. **Exit conditions** — reviewer marks task complete when satisfied. Deadlock after 2 rounds escalates to user.
6. **Composability** — commands chain multiple loops sequentially

## Bugfix Command Redesign

Hybrid: subagents for investigation, team for fix+review.

### Pre-team phase (subagents)

1. Dispatch `bugfix:reproducer` — get reproduction report
2. Dispatch `bugfix:root-cause-analyst` — get root cause + fix proposal
3. Present findings to user, get confirmation

### Team phase (do-loop)

**Loop 1: Fix**

| Teammate | Role | Context |
|----------|------|---------|
| fixer | Implements fix based on root cause analysis | Reproduction report + root cause + fix proposal |
| reviewer | Reviews fix for correctness, edge cases, style | Same + do-loop protocol |

Iterate until reviewer approves (max 3 rounds).

**Loop 2: Regression test**

Fixer writes regression test (guided by `regression-test` skill). Reviewer reviews. One mini-loop.

**Exit**: main session summarizes, user decides whether to commit.

## Feature Development Command Redesign

Three sequential do-loops after discovery/exploration.

### Phase 1: Discovery + Exploration (no team)

1. Gather requirements from user (command-level)
2. Parallel `Explore` agents for codebase analysis (subagents)
3. Clarifying questions via `clarifying-questions` skill (hard stop)

### Phase 2: Architecture do-loop

| Teammate | Role | Context |
|----------|------|---------|
| architect | Designs architecture using SOLID/Clean Arch/DDD/Patterns skills | Requirements + exploration results |
| arch-reviewer | Reviews for feasibility, patterns compliance, gaps | Same + do-loop protocol |

Loop until approved. User confirms blueprint (hard stop).

### Phase 3: Implementation do-loop

| Teammate | Role | Context |
|----------|------|---------|
| implementer | Writes code per blueprint | Blueprint + codebase conventions |
| code-reviewer | Reviews for bugs, security, architecture violations | Blueprint + do-loop protocol |

Loop until approved.

### Phase 4: Test do-loop

| Teammate | Role | Context |
|----------|------|---------|
| test-writer | Writes unit/integration/e2e tests | Implementation + testing skills |
| test-reviewer | Reviews test quality and coverage | Do-loop protocol |

Loop until approved.

**Exit**: main session produces summary.

## Review Plugin

Unchanged. Parallel independent reviewers don't need team communication.

## Files Changed

### New files
- `plugins/shared/.claude-plugin/plugin.json`
- `plugins/shared/skills/do-loop.md`

### Rewritten files
- `plugins/bugfix/commands/bugfix.md`
- `plugins/feature-development/commands/feature.md`

### Unchanged plugin files
- `plugins/bugfix/agents/reproducer.md`
- `plugins/bugfix/agents/root-cause-analyst.md`
- `plugins/bugfix/skills/regression-test.md`
- `plugins/feature-development/agents/code-architect.md`
- All feature-development skills
- Entire review plugin

### Sync files to update
- `.claude-plugin/marketplace.json` — add shared plugin, bump versions
- `CLAUDE.md` — 5 plugins, 12 skills
- `README.md` — add shared plugin section, update bugfix/feature sections
- `QUICKSTART.md` — update plugin table, workflow descriptions
- `plugins/bugfix/README.md` — rewrite for team workflow
- `plugins/bugfix/.claude-plugin/plugin.json` — bump version
- `plugins/feature-development/README.md` — rewrite for team workflow
- `plugins/feature-development/.claude-plugin/plugin.json` — bump version

## Counts After

- 5 plugins (was 4, +shared)
- 13 agents (unchanged)
- 8 commands (unchanged)
- 12 skills (was 11, +do-loop)
