# CC Plugins

Core development workflow plugins for Claude Code with specialized agents, Task tool orchestration, and architecture skills.

## Overview

CC Plugins provides **6 plugins with 10 specialized agents, 7 commands, and 19 skills** covering software development workflows and certification exam preparation.

Bugfix and feature-development plugins use **Claude Code Agent Teams** for iterative do-loops (doer+critic pairs). Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`.

All agents use YAML frontmatter for proper Claude Code registration and are callable via `Task(subagent_type="plugin:agent")`.

## Plugins

### Shared (Infrastructure)

Reusable agent team infrastructure. Dependency for bugfix and feature-development.

| Component | Type | Purpose |
|-----------|------|---------|
| `do-loop` | Skill | Iteration protocol for doer+critic agent team loops |

---

### Feature Development

**Command**: `/feature "Add OAuth2 authentication"`

Discovery → Exploration → Questions → Architecture Loop → Implementation Loop → Test Loop → Summary

| Component | Type | Purpose |
|-----------|------|---------|
| `code-architect` | Agent | Architecture design reference (SOLID, Clean Arch, Patterns, DDD) |
| `solid-principles` | Skill | SOLID design principles |
| `clean-architecture` | Skill | Layered architecture independent of frameworks |
| `design-patterns` | Skill | Common design patterns |
| `domain-driven-design` | Skill | DDD strategic and tactical patterns |
| `clarifying-questions` | Skill | Structured ambiguity resolution |
| `review-loop` | Skill | Code review guidelines for reviewer teammate |
| `testing-loop` | Skill | Test writing guidelines for test-writer teammate |

---

### Review

**Command**: `/review`, `/review --commit <sha>`, `/review --pr <number>`

Two-phase review: investigate first (broad tools), then 4 specialized reviewers analyze in parallel (narrow tools). Reports only issues with confidence >= 80.

| Component | Type | Purpose |
|-----------|------|---------|
| `diff-investigator` | Agent | Investigate uncommitted changes |
| `commit-investigator` | Agent | Investigate a specific commit |
| `pr-investigator` | Agent | Investigate a pull request |
| `security-reviewer` | Agent | Injection, auth, secrets, input validation, crypto |
| `architecture-reviewer` | Agent | Dependencies, layers, coupling, boundaries |
| `performance-reviewer` | Agent | N+1 queries, indexes, O(n²), resource leaks |
| `bug-reviewer` | Agent | Logic errors, type issues, error handling, data integrity |

---

### Bugfix

**Command**: `/bugfix "Login fails with special characters"`

Investigate (subagents) → Fix+Review (team loop) → Test+Review (team loop) → Verify

| Component | Type | Purpose |
|-----------|------|---------|
| `reproducer` | Agent | Reproduce bugs, create failing tests |
| `root-cause-analyst` | Agent | Trace execution, identify root cause, propose fix |
| `regression-test` | Skill | Writing effective regression tests |

---

### QA (Quality Assurance)

**Command**: `/qa <prompt>`

Single orchestrator with dynamic skill discovery, prompt-based routing, and do-loop agent teams. Routes to run, write, explore, or convert workflows based on your prompt. Bare `/qa` runs all tests.

| Component | Type | Purpose |
|-----------|------|---------|
| `unit-testing` | Skill | Unit testing patterns and framework detection |
| `integration-testing` | Skill | Integration testing patterns |
| `e2e-testing` | Skill | E2E and browser testing with Playwright |

---

### Certification

**Commands**: `/training`, `/quiz`, `/analysis`, `/update`

Claude Certified Architect – Foundations exam preparation. Interactive training by domain, mock exam simulation with weighted scoring, performance analysis with adaptive study recommendations, and content freshness checks.

| Component | Type | Purpose |
|-----------|------|---------|
| `domain-1-agentic-architecture` | Skill | Training curriculum for Domain 1 (27%) |
| `domain-2-tool-design-mcp` | Skill | Training curriculum for Domain 2 (18%) |
| `domain-3-claude-code-config` | Skill | Training curriculum for Domain 3 (20%) |
| `domain-4-prompt-engineering` | Skill | Training curriculum for Domain 4 (20%) |
| `domain-5-context-management` | Skill | Training curriculum for Domain 5 (15%) |
| `scoring` | Skill | Scoring formula and question selection algorithm |
| `study-path` | Skill | Adaptive study recommendation engine |

---

## Installation

### Using the Plugin Command (Recommended)

```bash
cd your-project
claude

# Install a specific plugin
/plugin install https://github.com/avovello/cc-plugins.git#plugins/review
```

### Manual Installation

```bash
git clone https://github.com/avovello/cc-plugins.git
cp -r cc-plugins/plugins/review .claude/plugins/
```

## Plugin Architecture

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── commands/
│   └── command-name.md       # Slash command (frontmatter + orchestration)
├── agents/
│   └── agent-name.md         # Agent (YAML frontmatter + system prompt)
└── skills/
    └── skill-name.md         # Skill (YAML frontmatter + reference content)
```

### Agent Frontmatter Format

```yaml
---
name: agent-name
description: What this agent does and when to use it
tools: Read, Glob, Grep, Bash
model: opus
maxTurns: 6
color: blue
---
```

## Task Tool Orchestration

Agents are invoked via the Task tool:

```
Task(subagent_type="feature-development:code-architect", prompt="Design architecture for [feature]")
Task(subagent_type="review:diff-investigator", prompt="Investigate uncommitted changes")
Task(subagent_type="review:security-reviewer", prompt="Review for security issues: [report]")
Task(subagent_type="bugfix:reproducer", prompt="Reproduce this bug: [description]")
Task(subagent_type="bugfix:root-cause-analyst", prompt="Analyze root cause: [reproduction report]")
```

## Version

2.4.0

## License

MIT License - see [LICENSE](LICENSE)
