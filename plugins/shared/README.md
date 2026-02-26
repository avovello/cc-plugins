# Shared Plugin

Reusable agent team infrastructure for cc-plugins. Dependency for bugfix and feature-development plugins.

> **Requires**: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

## Installation

```bash
/plugin install https://github.com/avovello/cc-plugins.git#plugins/shared
```

Install before bugfix or feature-development plugins.

## Skills (1)

| Skill | Purpose |
|-------|---------|
| `do-loop` | Iteration protocol for doer+critic agent team pairs — spawning, messaging, review rounds, exit conditions |

## What the do-loop skill covers

1. **Team spawning** — how to describe teammates (role, model, context)
2. **Task structure** — acceptance criteria, scope, quality bar
3. **Communication** — structured feedback format between doer and critic
4. **Iteration** — max 3 rounds, escalation rules
5. **Exit conditions** — approval, deadlock handling, user override
6. **Composability** — chaining sequential loops

## Version
1.0.0
