# QA Plugin Redesign

## Problem

The QA plugin has overlapping responsibilities:
- 5 commands (`/qa`, `/unit`, `/integration`, `/e2e`, `/write-tests`) where 4 are thin wrappers around the same 2 agents
- 3 skills (`unit-testing`, `integration-testing`, `e2e-testing`) orphaned — not referenced by any command or agent
- `/write-tests` generates code with no review step, unlike `/bugfix` and `/feature` which use do-loops
- Single-agent dispatches instead of iterative team workflows

## Design

### Structure

```
qa/
├── .claude-plugin/plugin.json
├── commands/
│   └── qa.md              # Single orchestrator command
├── skills/
│   ├── unit-testing.md    # Domain knowledge + routing description
│   ├── integration-testing.md
│   └── e2e-testing.md     # Includes browser/Playwright guidance
└── README.md
```

- **0 agents** — all work done by team teammates equipped with skills
- **1 command** — `/qa <prompt>` handles everything
- **3+ skills** — discoverable at runtime (plugin skills + project skills)

### Deleted

- `commands/unit.md`, `commands/integration.md`, `commands/e2e.md`, `commands/write-tests.md`
- `agents/test-writer.md`, `agents/test-runner.md`, `agents/browser-tester.md`

### Command Routing

```
/qa <prompt>
  │
  ├─ Step 1: Discover skills
  │    Glob for all available testing/QA skills:
  │    - Plugin skills: plugins/qa/skills/*.md
  │    - Project skills: .claude/skills/*.md, skills/*.md
  │    Read each skill's frontmatter (name + description)
  │
  ├─ Step 2: Match prompt to skills
  │    Present discovered skills + user prompt to the LLM
  │    "Given these available skills and the user's request,
  │     which skill(s) apply?"
  │
  ├─ Step 3: Determine activity
  │    From prompt: run, write, or explore?
  │    If ambiguous → ask the user
  │
  ├─ Step 4: Dispatch do-loop teams
  │    Spawn doer+critic with matched skill context
  │
  └─ Bare /qa (no prompt) → skip discovery, run all tests
```

### Do-Loop Workflows

#### Write workflow: write → review → fix → verify

```
Loop 1: Write + Review
  ├─ Doer (opus): Write tests using [matched skill] guidance
  ├─ Critic (opus): Review test quality — coverage, assertions,
  │                  patterns, edge cases
  └─ Max 3 rounds

Loop 2: Verify
  ├─ Run the written tests
  ├─ If failures → mini do-loop:
  │    Doer: fix the tests
  │    Critic: re-run and verify
  └─ Max 2 rounds
```

#### Run workflow: run → diagnose → fix → re-run

```
Step 1: Run tests

If all pass → report results, done.

If failures → Loop:
  ├─ Doer (opus): Analyze failures, fix code or tests
  ├─ Critic (opus): Re-run tests, verify fixes don't
  │                  break other tests
  └─ Max 3 rounds
```

#### Explore workflow: explore → record → generate → verify

```
Step 1: Browser exploration (teammate with e2e-testing skill)
  └─ Records actions in structured format

Loop: Convert + Review
  ├─ Doer: Convert recorded actions to automated tests
  │         using [e2e-testing skill]
  ├─ Critic: Review test quality, run tests
  └─ Max 3 rounds
```

### Skills Architecture

Skills serve two purposes:
1. **Routing** — their `description` field is matched against user prompts
2. **Domain knowledge** — their content guides teammates during do-loops

Skills are discovered dynamically, so project-specific skills (e.g., `contract-testing`, `visual-regression`) are auto-discovered and available without plugin changes.

Skill descriptions must be optimized for intent matching:
```yaml
---
name: unit-testing
description: Unit testing for isolated function and component testing.
  Use when running, writing, or reviewing unit tests, mocking
  dependencies, or measuring code coverage.
---
```

### Prerequisites

- Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` for do-loop workflows
- Without agent teams, falls back to single-pass execution (no review loop)
- Chrome/Playwright tools built into Claude Code — no MCP configuration needed

### Sync Checklist

When implementing, update all of:
1. `plugins/qa/.claude-plugin/plugin.json` — version, description (0 agents, 1 command)
2. `plugins/qa/README.md` — new structure, workflows
3. `.claude-plugin/marketplace.json` — qa entry + top-level version
4. `README.md` — root project docs (agent counts, plugin descriptions)
5. `QUICKSTART.md` — updated qa section
6. `CLAUDE.md` — project structure table (qa: 0 agents, 1 command, 3 skills)
