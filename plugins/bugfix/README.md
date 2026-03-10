# Bugfix Plugin

Systematic bug fixing with reproduction, root cause analysis, and team-based fix+review loops.

> **Requires**: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

## Installation
```bash
/plugin install https://github.com/avovello/cc-plugins.git#plugins/bugfix
```

## Usage
```bash
/bugfix "Users can't upload files larger than 5MB"
```

## Agents (2)

Used in the investigation phase (subagents, pre-team):

| Agent | Purpose | Tools |
|-------|---------|-------|
| `reproducer` | Reproduce bugs, create failing tests | Read, Glob, Grep, Bash |
| `root-cause-analyst` | Trace execution, identify root cause, assess impact, propose fix | Read, Glob, Grep, Bash |

## Skills (1 local + 1 from shared plugin)

| Skill | Source | Purpose |
|-------|--------|---------|
| `regression-test` | bugfix | Writing effective regression tests — naming, structure, coverage |
| `do-loop` | shared | Iteration protocol for doer+critic agent team loops |

## Workflow

```
Investigate (subagents) → Fix+Review (team loop) → Test+Review (team loop) → Verify
```

1. **Reproduce** — `reproducer` subagent creates failing test or documents exact steps
2. **Analyze** — `root-cause-analyst` subagent traces execution, identifies cause, proposes fix
3. **Confirm** — present findings to user, get confirmation to proceed
4. **Fix loop** — fixer+reviewer teammates iterate per do-loop protocol (max 3 rounds)
5. **Test loop** — test-writer+test-reviewer teammates write and review regression tests (1-2 rounds)
6. **Verify** — run tests, summarize changes, user decides whether to commit

## Design Decisions

- **Hybrid subagent+team**: Investigation is sequential and self-contained (subagents). Fix+review needs back-and-forth (agent teams).
- **Reproducer stays a subagent**: Fire-and-forget is fine — it just needs to find the bug and report evidence.
- **No fix-implementer agent**: Replaced by fixer teammate who gets real-time feedback from reviewer teammate.
- **Depends on shared plugin**: Uses the `do-loop` skill for team iteration protocol.

## Dependencies

- **shared plugin**: provides `do-loop` skill

## Related Plugins

- **QA Plugin**: `/qa` command for additional test scenarios beyond the bugfix scope
- **Shared Plugin**: `do-loop` iteration protocol

## Version
2.1.0
