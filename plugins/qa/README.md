# QA Plugin

Quality assurance orchestrator with dynamic skill discovery, prompt-based routing, and do-loop agent teams for test writing, execution, and browser testing.

## Features

- **Single Entry Point** - One `/qa <prompt>` command handles all QA workflows
- **Dynamic Skill Discovery** - Discovers plugin skills and project-specific skills automatically
- **Prompt-Based Routing** - Routes to run, write, explore, or convert workflows based on your prompt
- **Do-Loop Agent Teams** - Iterative doer+critic pairs for test writing, fixing, and review
- **Browser Testing** - Interactive exploration and action recording via built-in Chrome/Playwright tools
- **Multi-Framework** - Jest, Vitest, pytest, PHPUnit, Playwright

## Installation

```bash
/plugin install https://github.com/avovello/cc-plugins.git#plugins/qa
```

## Prerequisites

- Test framework installed (Jest/Vitest/pytest/PHPUnit)
- Node.js 18+ (for Playwright E2E testing)
- `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` for do-loop workflows (falls back to single-pass without it)

## Quick Start

```bash
/qa                                          # Run all tests (default)
/qa run unit tests                           # Run unit tests
/qa run integration tests for user API       # Run specific integration tests
/qa write unit tests for src/services/auth   # Write tests with review loop
/qa test login flow in browser               # Interactive browser testing
/qa convert recorded actions to tests        # Convert browser recordings to E2E tests
```

## Command

| Command | Description |
|---------|-------------|
| `/qa <prompt>` | QA orchestrator — routes to the right workflow based on your prompt |

### Activities

| Activity | Trigger Examples | What It Does |
|----------|-----------------|--------------|
| **run** | `run unit tests`, `run integration tests` | Execute existing tests, fix failures via do-loop |
| **write** | `write tests for auth service` | Generate tests with iterative quality review |
| **explore** | `test login flow in browser` | Interactive browser testing with action recording |
| **convert** | `convert recorded actions to tests` | Transform recorded browser actions to Playwright tests |

Bare `/qa` with no prompt defaults to running all tests (unit, integration, E2E) sequentially.

## Skills

| Skill | Description |
|-------|-------------|
| `unit-testing` | Unit test guidance, framework detection, Arrange-Act-Assert patterns |
| `integration-testing` | Integration test guidance for APIs, databases, service interactions |
| `e2e-testing` | E2E and browser testing with Playwright, action recording, selectors |

Skills are discovered dynamically at runtime. The command also searches for project-specific skills in `.claude/skills/*.md` and `skills/*.md`, so your project can extend the QA plugin with custom testing knowledge.

## Workflows

### Run Workflow

1. Detect test framework using the matched skill's detection table
2. Execute the test command
3. If tests pass, report results
4. If tests fail, spawn a fixer+verifier do-loop (max 3 rounds) to fix failures
5. Report final results with fixes applied

### Write Workflow

Two chained do-loops:

1. **Write + Review loop** - test-writer generates tests, test-reviewer checks quality (max 3 rounds)
2. **Verify loop** - run written tests, fix any failures (max 2 rounds)

### Explore Workflow

1. Browser explorer navigates the app, interacts with elements, records actions
2. After exploration, offers to convert recorded actions to automated tests

### Convert Workflow

Converts previously recorded browser actions to automated Playwright tests using the write workflow with recorded actions as context.

## Framework Support

| Language | Unit | Integration | E2E |
|----------|------|-------------|-----|
| JavaScript/TypeScript | Jest, Vitest | supertest | Playwright |
| Python | pytest | pytest | Playwright |
| PHP | PHPUnit | PHPUnit | Playwright |

## Structure

```
qa/
├── .claude-plugin/plugin.json   # Plugin manifest
├── commands/qa.md               # Single orchestrator command
├── skills/
│   ├── unit-testing.md          # Unit test domain knowledge
│   ├── integration-testing.md   # Integration test domain knowledge
│   └── e2e-testing.md           # E2E + browser testing domain knowledge
└── README.md
```

## Version

- **Version**: 3.0.0
- **Category**: Quality
- **Keywords**: testing, qa, agent-teams, do-loop, skill-discovery
