# CC Plugins

5 plugins, 13 agents, 8 commands, 12 skills for Claude Code development workflows.

> Agent teams (experimental) require `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`.

## Project Structure

```
plugins/
├── shared/               # 0 agents, 1 skill: do-loop (team iteration protocol)
├── bugfix/               # 2 agents: reproducer, root-cause-analyst + 1 skill + team do-loops
├── feature-development/  # 1 agent: code-architect + 7 skills + team do-loops
├── qa/                   # 3 agents: test-writer, test-runner, browser-tester + 3 skills
└── review/               # 7 agents: 3 investigators + 4 reviewers
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
4. `README.md` — root project docs (agent counts, plugin descriptions)
5. `QUICKSTART.md` — user-facing quick start guide

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
allowed-tools: Task, Bash(git diff:*)   # Task is required if command dispatches agents
---
```

Commands that dispatch agents via Task() MUST include `Task` in `allowed-tools`.

## Orchestration Pattern

Commands orchestrate agents in phases:
1. **Phase 1**: Investigation/analysis (single agent, broad tools)
2. **Phase 2**: Specialized work in parallel (multiple agents, narrow tools)
3. **Phase 3**: Validation and consolidation

Pass results between phases via agent prompts using `Task(subagent_type="plugin:agent")`.

## Do-Loop Pattern (Agent Teams)

Bugfix and feature-development use agent teams for iterative work. The `shared:do-loop` skill defines the protocol:

```
command → spawn doer+critic teammates → iterate (max 3 rounds) → next loop
```

- Doer does the work, critic reviews
- Structured feedback: `file:line — issue — fix`
- Max 3 rounds, then escalate to user
- Commands chain multiple loops sequentially (architecture → implementation → tests)
- Subagents still used for non-iterative work (investigation, exploration)

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
