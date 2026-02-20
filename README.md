# CC Plugins

Core development workflow plugins for Claude Code with properly registered agents, Task tool orchestration, and architecture skills.

## Overview

CC Plugins provides **4 plugins with 7 specialized agents** covering the essential software development workflows: feature development, code review, bug fixing, and quality assurance.

All agents use YAML frontmatter for proper Claude Code registration and are callable via `Task(subagent_type="plugin:agent")`. Codebase exploration uses Claude Code's built-in `Explore` agent.

## Plugins

### Feature Development

**Command**: `/feature "Add OAuth2 authentication"`

7-phase workflow: Discovery → Exploration → Clarifying Questions → Architecture Design → Implementation → QA-DEV-REVIEW Loops → Summary

| Component | Type | Purpose |
|-----------|------|---------|
| `code-architect` | Agent | Architecture design with SOLID, Clean Architecture, Design Patterns, DDD |
| `solid-principles` | Skill | SOLID design principles |
| `clean-architecture` | Skill | Layered architecture independent of frameworks |
| `design-patterns` | Skill | Common design patterns |
| `domain-driven-design` | Skill | DDD strategic and tactical patterns |
| `clarifying-questions` | Skill | Structured ambiguity resolution |
| `review-loop` | Skill | Review-fix-rereview automation |
| `testing-loop` | Skill | Test-fix-retest automation |

---

### Review

**Command**: `/review`

Single comprehensive reviewer that detects what's relevant from the diff and covers architecture, security, performance, and language-specific concerns. Reports only issues with confidence >= 80.

| Component | Type | Purpose |
|-----------|------|---------|
| `code-reviewer` | Agent | Multi-perspective code review with confidence scoring |

---

### Bugfix

**Command**: `/bugfix "Login fails with special characters"`

Systematic bug fixing: Reproduce → Trace → Root Cause → Impact → Fix Plan → Implement → Verify

| Component | Type | Purpose |
|-----------|------|---------|
| `root-cause-analyst` | Agent | Reproduce, trace, assess impact, plan fix |
| `fix-implementer` | Agent | Apply code changes per fix plan |

---

### QA (Quality Assurance)

**Commands**: `/qa`, `/unit`, `/integration`, `/e2e`, `/write-tests`

Test writing, execution, and browser testing for unit, integration, and E2E workflows.

| Component | Type | Purpose |
|-----------|------|---------|
| `test-writer` | Agent | Generate test code with fixtures and mocks |
| `test-runner` | Agent | Execute test suites, report results |
| `browser-tester` | Agent | Interactive browser testing via Playwright MCP |
| `unit-testing` | Skill | Unit testing patterns |
| `integration-testing` | Skill | Integration testing patterns |
| `e2e-testing` | Skill | E2E testing with Playwright |

---

## Installation

### Using the Plugin Command (Recommended)

```bash
cd your-project
claude

# Install a specific plugin
/plugin install https://github.com/avovello/claude-code.git#plugins/feature-development
```

### Manual Installation

```bash
git clone https://github.com/avovello/claude-code.git
cp -r claude-code/plugins/feature-development .claude/plugins/
```

## Plugin Architecture

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── commands/
│   └── command-name.md       # Slash command (frontmatter + markdown)
├── agents/
│   └── agent-name.md         # Agent (YAML frontmatter + task instructions)
└── skills/
    └── skill-name.md         # Skill (YAML frontmatter + reference content)
```

### Agent Frontmatter Format

```yaml
---
name: agent-name
description: What this agent does and when to use it
tools: Read, Glob, Grep, Bash
model: sonnet
maxTurns: 12
---
```

## Task Tool Orchestration

Agents are invoked via the Task tool:

```
Task(subagent_type="feature-development:code-architect", prompt="Design architecture for [feature]")
Task(subagent_type="review:code-reviewer", prompt="Review changes in [files]")
Task(subagent_type="bugfix:root-cause-analyst", prompt="Investigate: [bug report]")
Task(subagent_type="qa:test-runner", prompt="Run test suite")
```

For codebase exploration, use Claude Code's built-in Explore agent:

```
Task(subagent_type="Explore", prompt="Trace how [feature] works end-to-end")
```

## Version

2.0.0

## License

MIT License - see [LICENSE](LICENSE)
