# CC Plugins

6 plugins, 10 agents, 8 commands, 20 skills for Claude Code development workflows.

> Agent teams (experimental) require `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`.

## Project Structure

```
plugins/
├── shared/               # 0 agents, 1 skill: do-loop (team iteration protocol)
├── bugfix/               # 2 agents: reproducer, root-cause-analyst + 1 skill + team do-loops
├── feature-development/  # 1 agent: code-architect + 7 skills + team do-loops
├── qa/                   # 0 agents, 1 command, 3 skills + team do-loops
├── review/               # 7 agents: 3 investigators + 4 reviewers
└── certification/        # 0 agents, 4 commands, 8 skills (exam prep)
```

Each plugin follows:
```
plugin-name/
├── .claude-plugin/plugin.json   # Manifest (name, description, version, author, keywords)
├── agents/*.md                  # Agent files (YAML frontmatter + system prompt)
├── commands/*.md                # Slash commands (YAML frontmatter + orchestration)
├── skills/*.md                  # Skills (optional)
└── README.md
```

## Files That Must Stay In Sync

When changing a plugin, update ALL of these:

1. `plugins/<name>/.claude-plugin/plugin.json` — plugin manifest (version, description)
2. `plugins/<name>/README.md` — plugin docs
3. `.claude-plugin/marketplace.json` — root marketplace registry (version, description for the plugin entry AND the top-level version)
4. `CLAUDE.md` — project conventions (plugin counts, structure tree)
5. `README.md` — root project docs (agent counts, plugin descriptions)
6. `QUICKSTART.md` — user-facing quick start guide

Missing any of these causes version/description drift. The marketplace.json is especially easy to forget.

## Agent Conventions

### Frontmatter (required fields)

```yaml
---
name: lowercase-with-hyphens    # 3-50 chars, alphanumeric start/end
description: When and why to use this agent
tools: Read, Glob, Grep         # Minimum needed — least privilege
model: opus                     # opus for most, haiku for lightweight validation
maxTurns: 6                     # Keep low — agents should be focused
color: blue                     # Distinct per agent role
---
```

### Tool Assignment — Least Privilege

| Role | Tools | Rationale |
|------|-------|-----------|
| Investigator/explorer | Bash, Read, Glob, Grep | Needs git commands + filesystem exploration |
| Code reviewer (read-only) | Read, Grep | Forces focus on analysis, not wandering |
| Architecture reviewer | Read, Glob, Grep | Needs Glob to verify project structure |
| Code modifier | Read, Write, Edit, Glob, Grep, Bash | Full access to make changes |
| Test runner | Read, Glob, Grep, Bash | Needs Bash to execute test commands |

No review agent should have Write or Edit. Review is read-only.

### Command Frontmatter

```yaml
---
description: What this command does
allowed-tools: Task, Bash       # Task required if command dispatches agents or spawns teams
---
```

Commands that dispatch agents via Task() MUST include `Task` in `allowed-tools`.
Use scoped `Bash(git diff:*)` when the command should only run specific shell commands (e.g. review).
Use unscoped `Bash` when the command needs to run tests, linters, or arbitrary verification (e.g. bugfix, feature).

## Orchestration Patterns

### Subagent Pattern (review, investigation phases)

Commands dispatch focused subagents that run and return results:
1. **Phase 1**: Investigation/analysis (single agent, broad tools)
2. **Phase 2**: Specialized work in parallel (multiple agents, narrow tools)
3. **Phase 3**: Validation and consolidation

Pass results between phases via agent prompts using `Task(subagent_type="plugin:agent")`.

### Do-Loop Pattern (bugfix fix/test, feature-dev architecture/implementation/tests)

Commands spawn agent team pairs (doer+critic) that iterate until the critic approves. The `shared:do-loop` skill defines the protocol:

```
command → spawn doer+critic teammates → iterate (max 3 rounds) → next loop
```

- Doer does the work, critic reviews via structured feedback: `file:line — issue — fix`
- Tasks use native team states: `pending → in_progress → completed`
- Max 3 rounds per loop, then escalate to user
- Commands chain multiple loops sequentially
- Subagents still used for non-iterative work (investigation, exploration)

### Hybrid Pattern (bugfix)

Some commands use both: subagents for investigation phases, then agent teams for iterative fix+review.

## Bugfix Plugin Architecture

```
/bugfix → reproducer (subagent) → root-cause-analyst (subagent) → user confirms
       → fixer+reviewer (team loop) → test-writer+test-reviewer (team loop) → verify
```

- Investigation is sequential subagents (fire-and-forget)
- Fix and test phases use agent team do-loops (doer+critic pairs)
- User confirms findings before fix phase begins

## Feature Development Plugin Architecture

```
/feature → discovery → explore (subagents) → questions (hard stop)
        → architect+arch-reviewer (team loop) → user confirms blueprint
        → implementer+code-reviewer (team loop)
        → test-writer+test-reviewer (team loop) → summary
```

- Phases 1-3: command-level + parallel Explore subagents
- Phases 4-6: three sequential do-loops with user approval gate after architecture

## Review Plugin Architecture

```
/review → diff-investigator → [security, architecture, performance, bug] reviewers → validate → report
```

- Investigators produce a structured Investigation Report
- Reviewers receive the report and do targeted analysis
- Only issues with confidence >= 80 are reported
- Quality gates: Critical (0 allowed), High (≤2), Medium (≤10)

## Key Rules

- Agent system prompts: second person ("You are...", "Your job is...")
- No Claude mentions in agent prompts or outputs
- Review is read-only — never generate commits or push code
- Confidence >= 80 threshold for all review findings
- Provide concrete fixes for every reported issue
