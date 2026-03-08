# QA Plugin Redesign — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Redesign the QA plugin from 5 commands + 3 agents + 3 orphaned skills into 1 smart orchestrator command + 0 agents + 3 routing-ready skills with do-loop team workflows.

**Architecture:** Single `/qa <prompt>` command performs dynamic skill discovery (plugin + project skills), matches user intent to skills, determines activity (run/write/explore), and dispatches do-loop agent teams equipped with the matched skill context. Bare `/qa` defaults to running all tests.

**Tech Stack:** Claude Code plugins (markdown commands, skills with YAML frontmatter, agent teams with do-loop protocol)

---

### Task 1: Rewrite unit-testing skill

Rewrite from human-facing reference doc to routing-ready skill with intent-matching description and actionable agent guidance.

**Files:**
- Modify: `plugins/qa/skills/unit-testing.md`

**Step 1: Read the current skill**

Read: `plugins/qa/skills/unit-testing.md`

**Step 2: Rewrite the skill**

Replace entire contents with:

```markdown
---
name: unit-testing
description: Unit testing for isolated function and component testing. Use when running, writing, or reviewing unit tests, testing individual functions or class methods, mocking dependencies, or measuring code coverage with Jest, Vitest, pytest, or PHPUnit.
---

# Unit Testing

Guide for teammates working with unit tests — writing, running, or reviewing.

## Framework Detection

Check for configuration to determine the framework:

| File present | Framework | Run command |
|-------------|-----------|-------------|
| `jest.config.*` or `"jest"` in package.json | Jest | `npm test -- tests/unit/` |
| `vitest.config.*` | Vitest | `npx vitest run tests/unit/` |
| `pytest.ini` or `[tool.pytest]` in pyproject.toml | pytest | `pytest tests/unit/` |
| `phpunit.xml` | PHPUnit | `./vendor/bin/phpunit tests/Unit/` |

Add `--coverage` flag when coverage is requested.

## Writing Tests

Follow Arrange-Act-Assert. One assertion focus per test.

- Name tests descriptively: `should [expected behavior] when [condition]`
- Mock at boundaries — mock external deps, not internal implementation
- Use factories for test data — don't hardcode
- Test behavior, not implementation — tests shouldn't break on refactor

Match existing test conventions in the codebase (naming, directory structure, imports).

## Reviewing Tests

Check for:
- Coverage of happy path, error cases, boundary conditions
- Proper mocking strategy (no over-mocking)
- Descriptive test names
- Arrange-Act-Assert structure
- No hardcoded test data

## Coverage Targets

Statements 80%+, Branches 75%+, Functions 80%+, Lines 80%+.
```

**Step 3: Verify frontmatter parses correctly**

Run: `head -3 plugins/qa/skills/unit-testing.md`
Expected: `---`, `name: unit-testing`, `description: Unit testing...`

**Step 4: Commit**

```bash
git add plugins/qa/skills/unit-testing.md
git commit -m "Rewrite unit-testing skill for routing and agent guidance"
```

---

### Task 2: Rewrite integration-testing skill

**Files:**
- Modify: `plugins/qa/skills/integration-testing.md`

**Step 1: Read the current skill**

Read: `plugins/qa/skills/integration-testing.md`

**Step 2: Rewrite the skill**

Replace entire contents with:

```markdown
---
name: integration-testing
description: Integration testing for API endpoints, database operations, and service interactions. Use when running, writing, or reviewing integration tests, testing HTTP endpoints, CRUD operations, external service connections, or component interactions.
---

# Integration Testing

Guide for teammates working with integration tests — writing, running, or reviewing.

## Framework Detection

| File present | Framework | Run command |
|-------------|-----------|-------------|
| package.json with test:integration script | Node.js | `npm run test:integration` |
| `vitest.config.*` | Vitest | `npx vitest run tests/integration/` |
| `pytest.ini` or pyproject.toml | pytest | `pytest tests/integration/` |
| `phpunit.xml` | PHPUnit | `./vendor/bin/phpunit --testsuite Integration` |

## Writing Tests

- Set up test database/services in beforeAll/setUp
- Clean up in afterEach/tearDown — each test starts with clean state
- Test real component interactions — don't mock what you're integrating
- Validate request/response contracts for API tests

### Test Types

| Type | What to test | Where |
|------|-------------|-------|
| API | HTTP endpoints, status codes, response bodies | tests/integration/api/ |
| Database | CRUD operations, migrations, constraints | tests/integration/db/ |
| Service | External service connections, retry logic | tests/integration/services/ |

## Reviewing Tests

Check for:
- Proper setup/teardown (no state leakage between tests)
- Real component interactions (not over-mocked)
- Request/response contract validation
- Error response testing (400, 401, 404, 500)
- Database transaction rollback or cleanup
```

**Step 3: Commit**

```bash
git add plugins/qa/skills/integration-testing.md
git commit -m "Rewrite integration-testing skill for routing and agent guidance"
```

---

### Task 3: Rewrite e2e-testing skill

**Files:**
- Modify: `plugins/qa/skills/e2e-testing.md`

**Step 1: Read the current skill**

Read: `plugins/qa/skills/e2e-testing.md`

**Step 2: Rewrite the skill**

Replace entire contents with:

```markdown
---
name: e2e-testing
description: End-to-end testing and interactive browser testing with Playwright. Use when running, writing, or reviewing E2E tests, testing user workflows in a browser, exploring web apps interactively, recording browser actions, cross-browser testing, visual validation, or converting recorded actions to automated tests.
---

# E2E Testing

Guide for teammates working with end-to-end tests and browser testing — writing, running, exploring, or reviewing.

## Running Tests

```bash
npx playwright test                           # Run all E2E tests
npx playwright test tests/e2e/login.spec.ts   # Run specific test
npx playwright test --headed                  # With visible browser
npx playwright test --project=chromium        # Specific browser
```

## Writing Tests

- Use Page Object Model for selectors
- Test complete user workflows end-to-end
- Wait for network idle before assertions
- Capture screenshots at key assertion points

### Selector Priority

1. `[data-testid="..."]` — most stable
2. Role-based: `getByRole('button', { name: '...' })`
3. Text-based: `getByText('...')`
4. CSS selectors — last resort

## Interactive Browser Exploration

When exploring a web app interactively, use built-in browser tools:

1. Navigate to target URL
2. Take accessibility snapshot to understand page structure
3. Interact with elements (fill forms, click buttons)
4. Validate outcomes (check content, URLs, element state)
5. Capture screenshots at key states
6. Check console for JavaScript errors

### Recording Actions

Document all actions in structured format for conversion to automated tests:

```
## Recorded Actions: [Test Name]
1. navigate: /page
2. fill: input[name="field"] = "value"
3. click: button[type="submit"]
4. wait: .success-message
5. assert: URL contains "/expected"
6. screenshot: descriptive-name
```

## Responsive Testing

Test at multiple viewport sizes: Mobile (375x667), Tablet (768x1024), Desktop (1920x1080).

## Reviewing Tests

Check for:
- Complete user workflow coverage (not just click tests)
- Stable selectors (data-testid preferred)
- Proper waits (no arbitrary sleep)
- Screenshot evidence at key states
- Console error checking
- Responsive viewport coverage
```

**Step 3: Commit**

```bash
git add plugins/qa/skills/e2e-testing.md
git commit -m "Rewrite e2e-testing skill for routing and agent guidance"
```

---

### Task 4: Write the /qa orchestrator command

This is the core of the redesign — the single command that does skill discovery, routing, and do-loop orchestration.

**Files:**
- Modify: `plugins/qa/commands/qa.md`

**Step 1: Read the current command**

Read: `plugins/qa/commands/qa.md`

**Step 2: Rewrite the command**

Replace entire contents with:

```markdown
---
description: Quality assurance orchestrator — run tests, write tests, or explore in browser. Routes to the right workflow based on your prompt with dynamic skill discovery.
allowed-tools: Task, Bash, Glob, Read, Grep
---

# QA Command

Single entry point for all QA workflows. Discovers available testing skills, matches your prompt, and orchestrates do-loop agent teams.

> **Prerequisite**: Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` for do-loop workflows. Without it, falls back to single-pass execution.

## Usage

```bash
/qa                                          # Run all tests (default)
/qa run unit tests                           # Run unit tests
/qa run integration tests for user API       # Run specific integration tests
/qa write unit tests for src/services/auth   # Write tests with review loop
/qa test login flow in browser               # Interactive browser testing
/qa convert recorded actions to tests        # Convert browser recordings to E2E tests
```

## Workflow

### Step 1: Handle Bare /qa

If no prompt is provided, skip to "Run All Tests":
1. Detect test framework(s) in the project
2. Run tests sequentially: unit → integration → E2E
3. Report results and exit

### Step 2: Discover Skills

Find all available testing skills:

```
Glob: plugins/qa/skills/*.md
Glob: .claude/skills/*.md
Glob: skills/*.md
```

For each skill file found, read the YAML frontmatter to extract `name` and `description`.

Build a skills catalog:
```
Available skills:
- unit-testing: Unit testing for isolated function and component testing...
- integration-testing: Integration testing for API endpoints...
- e2e-testing: End-to-end testing and interactive browser testing...
- [any project-specific skills discovered]
```

### Step 3: Match and Route

Given the user's prompt and the skills catalog, determine:

1. **Which skill(s) match** — based on skill descriptions vs user prompt
2. **What activity** — one of:
   - **run**: Execute existing tests
   - **write**: Generate new test code
   - **explore**: Interactive browser testing
   - **convert**: Transform recorded actions to automated tests

If the activity is ambiguous, ask the user:
> "What would you like to do?"
> - Run existing tests
> - Write new tests
> - Explore in browser

### Step 4: Load Matched Skills

Load the matched skill(s) content. This provides the domain knowledge that will be passed to teammates.

### Step 5: Execute Workflow

Route to the appropriate workflow based on the determined activity.

---

## Run Workflow

Run existing tests using the matched skill's framework detection guidance.

**Step 1:** Detect test framework using the matched skill's detection table.

**Step 2:** Execute the appropriate test command via Bash.

**Step 3:** If all tests pass → report results, done.

**Step 4:** If tests fail and agent teams are available → spawn a fix loop:

Load the `do-loop` skill. Start a team of 2:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| fixer | Analyze test failures and fix the code or tests. Use the matched skill's guidance for test patterns. | opus | Failed test output + matched skill content + relevant source files |
| verifier | Re-run tests after each fix. Verify the fix doesn't break other tests. Check that the fix addresses root cause, not symptoms. | opus | Do-loop iteration protocol (max 3 rounds, structured feedback format, exit conditions) |

**Task**: `Fix failing tests — all tests pass, no regressions`

**Done means**: All previously failing tests pass, no new failures introduced.

**Step 5:** Report final results:

```markdown
## QA Report

### Test Results
| Suite | Passed | Failed | Duration |
|-------|--------|--------|----------|
| [type] | [n] | [n] | [time] |

### Status: PASSED / FIXED / FAILED

### Fixes Applied (if any)
- `path/to/file.ext` — [what was fixed]
```

---

## Write Workflow

Generate tests with iterative quality review.

Load the `do-loop` skill. Execute two chained loops:

### Loop 1: Write + Review

Start a team of 2:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| test-writer | Write tests using the matched skill's guidance. Follow Arrange-Act-Assert. Match existing test conventions. Cover happy path, error cases, edge cases. | opus | Matched skill content + source code to test + existing test patterns |
| test-reviewer | Review test quality: coverage, assertions, naming, mocking strategy, edge cases. Use structured feedback format. | opus | Matched skill content + do-loop iteration protocol (max 3 rounds, structured feedback format, exit conditions) |

**Task**: `Write [type] tests for [target] — comprehensive coverage with quality review`

**Done means**: Tests cover happy path, error cases, and edge cases. Follow existing conventions. Reviewer approves quality.

### Loop 2: Verify

Run the written tests.

If failures → spawn a mini fix loop (max 2 rounds):

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| fixer | Fix the failing tests. Determine if tests are wrong or if they found real bugs. | opus | Test output + written test files + source code |
| verifier | Re-run tests, confirm fixes are correct. | opus | Do-loop protocol (max 2 rounds) |

**Report:**

```markdown
## Test Generation Report

### Generated
- [test file path] — [count] tests covering [what]

### Quality Review
- Rounds: [n]
- Issues resolved: [list]

### Verification
- Status: PASSED / FIXED
```

---

## Explore Workflow

Interactive browser testing with action recording.

**Step 1:** Start a team with a browser explorer:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| explorer | Navigate the web app, interact with elements, capture screenshots, record all actions in structured format. Check console for JS errors. Test at multiple viewports. | opus | e2e-testing skill content + target URL |

The explorer records actions in structured format (see e2e-testing skill).

**Step 2:** After exploration, offer to convert recorded actions to automated tests.

If the user accepts → switch to **Write Workflow** with the recorded actions as input context, using the e2e-testing skill.

**Report:**

```markdown
## Browser Testing Report

### Session
- Target: [URL]
- Viewports tested: [list]

### Results
| Test Case | Status | Notes |
|-----------|--------|-------|
| [name] | PASS/FAIL | [observation] |

### Issues Found
- [severity]: [description] at [location]

### Recorded Actions
[structured action list — available for /qa convert recorded actions to tests]
```

---

## Convert Workflow

Convert previously recorded browser actions to automated Playwright tests.

Uses the **Write Workflow** with recorded actions as input context and the e2e-testing skill.

---

## Agents

This command uses no pre-defined agents. All work is done by team teammates spawned with skill context.

## Skills

| Skill | Purpose |
|-------|---------|
| `do-loop` (shared plugin) | Iteration protocol for doer+critic pairs |
| `unit-testing` | Unit test domain knowledge and framework detection |
| `integration-testing` | Integration test domain knowledge |
| `e2e-testing` | E2E + browser testing domain knowledge |
| Project-specific skills | Auto-discovered from project directories |
```

**Step 3: Verify frontmatter**

Run: `head -4 plugins/qa/commands/qa.md`
Expected: `---`, `description: Quality assurance...`, `allowed-tools: Task, Bash, Glob, Read, Grep`, `---`

**Step 4: Commit**

```bash
git add plugins/qa/commands/qa.md
git commit -m "Rewrite /qa as single orchestrator with skill discovery and do-loops"
```

---

### Task 5: Delete old commands and agents

Remove the files that are now absorbed into `/qa`.

**Files:**
- Delete: `plugins/qa/commands/unit.md`
- Delete: `plugins/qa/commands/integration.md`
- Delete: `plugins/qa/commands/e2e.md`
- Delete: `plugins/qa/commands/write-tests.md`
- Delete: `plugins/qa/agents/test-writer.md`
- Delete: `plugins/qa/agents/test-runner.md`
- Delete: `plugins/qa/agents/browser-tester.md`

**Step 1: Delete files**

```bash
git rm plugins/qa/commands/unit.md plugins/qa/commands/integration.md plugins/qa/commands/e2e.md plugins/qa/commands/write-tests.md
git rm plugins/qa/agents/test-writer.md plugins/qa/agents/test-runner.md plugins/qa/agents/browser-tester.md
```

**Step 2: Verify agents directory is empty**

```bash
ls plugins/qa/agents/
```

Expected: empty or directory doesn't exist.

**Step 3: Remove empty agents directory if it exists**

```bash
rmdir plugins/qa/agents/ 2>/dev/null || true
```

**Step 4: Commit**

```bash
git commit -m "Remove old QA commands and agents absorbed into /qa orchestrator"
```

---

### Task 6: Update plugin.json

**Files:**
- Modify: `plugins/qa/.claude-plugin/plugin.json`

**Step 1: Read current file**

Read: `plugins/qa/.claude-plugin/plugin.json`

**Step 2: Update**

Change version to `3.0.0` and update description to reflect the new architecture:

```json
{
  "name": "qa",
  "description": "Quality assurance orchestrator with dynamic skill discovery, prompt-based routing, and do-loop agent teams for test writing, execution, and browser testing",
  "version": "3.0.0",
  "author": {
    "name": "Claude Code Marketplace Contributors",
    "email": "noreply@anthropic.com"
  },
  "keywords": ["testing", "qa", "agent-teams", "do-loop", "skill-discovery"]
}
```

**Step 3: Commit**

```bash
git add plugins/qa/.claude-plugin/plugin.json
git commit -m "Bump qa plugin to v3.0.0 with new architecture"
```

---

### Task 7: Update QA README

**Files:**
- Modify: `plugins/qa/README.md`

**Step 1: Read current file**

Read: `plugins/qa/README.md`

**Step 2: Rewrite**

Replace entire contents to reflect the new architecture — 1 command, 0 agents, 3 skills, do-loop workflows, skill discovery.

Key sections:
- Features: skill discovery, do-loop teams, prompt routing
- Quick Start: `/qa` usage examples
- Command: `/qa <prompt>` with activity types
- Skills: 3 built-in + project-discoverable
- Workflows: run, write, explore, convert
- Prerequisites: agent teams env var
- Structure: show the simplified directory layout

**Step 3: Commit**

```bash
git add plugins/qa/README.md
git commit -m "Update QA README for v3.0.0 architecture"
```

---

### Task 8: Update marketplace.json

**Files:**
- Modify: `.claude-plugin/marketplace.json`

**Step 1: Read current file**

Read: `.claude-plugin/marketplace.json`

**Step 2: Update the qa entry and top-level version**

- Change qa `version` to `3.0.0`
- Change qa `description` to match plugin.json
- Update qa `keywords` to include `agent-teams`, `do-loop`, `skill-discovery`
- Bump top-level `version` and `metadata.version` to `2.3.0`

**Step 3: Commit**

```bash
git add .claude-plugin/marketplace.json
git commit -m "Update marketplace.json for qa v3.0.0"
```

---

### Task 9: Update root README.md

**Files:**
- Modify: `README.md`

**Step 1: Read current file**

Read: `README.md`

**Step 2: Update the QA section**

Replace the QA section with:
- Command: `/qa <prompt>` (single command)
- 0 agents — uses agent teams
- 3 skills: unit-testing, integration-testing, e2e-testing
- Updated Task tool examples (remove qa:test-runner, etc.)
- Update overview counts: 5 plugins, 10 agents (was 13), 12 skills (unchanged), 4 commands (was 8)

Also update the version at bottom to `2.3.0`.

**Step 3: Commit**

```bash
git add README.md
git commit -m "Update root README for qa v3.0.0 redesign"
```

---

### Task 10: Update QUICKSTART.md

**Files:**
- Modify: `QUICKSTART.md`

**Step 1: Read current file**

Read: `QUICKSTART.md`

**Step 2: Update QA references**

- Replace the "4. Run Tests" section with new `/qa` usage examples
- Remove references to `/unit`, `/integration`, `/e2e`, `/write-tests`
- Update the Available Plugins table: QA row shows 1 command, 0 agents
- Update header counts (10 agents, 4 commands)
- Add note about agent teams requirement for QA do-loops

**Step 3: Commit**

```bash
git add QUICKSTART.md
git commit -m "Update QUICKSTART for qa v3.0.0"
```

---

### Task 11: Update CLAUDE.md

**Files:**
- Modify: `CLAUDE.md`

**Step 1: Read current file**

Read: `CLAUDE.md`

**Step 2: Update project structure and counts**

- Update the header: `5 plugins, 10 agents, 4 commands, 12 skills`
- Update the structure table: `qa/` shows `0 agents, 1 command, 3 skills + team do-loops`
- Update the agent count references

**Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "Update CLAUDE.md project structure for qa v3.0.0"
```

---

### Task 12: Final verification

**Step 1: Verify file structure**

```bash
find plugins/qa -type f | sort
```

Expected:
```
plugins/qa/.claude-plugin/plugin.json
plugins/qa/README.md
plugins/qa/commands/qa.md
plugins/qa/skills/e2e-testing.md
plugins/qa/skills/integration-testing.md
plugins/qa/skills/unit-testing.md
```

**Step 2: Verify no broken references**

```bash
grep -r "qa:test-writer\|qa:test-runner\|qa:browser-tester" plugins/ --include="*.md"
grep -r "/unit\b\|/integration\b\|/e2e\b\|/write-tests" plugins/ README.md QUICKSTART.md CLAUDE.md --include="*.md"
```

Expected: No matches (all references updated or removed).

**Step 3: Verify JSON validity**

```bash
python3 -c "import json; json.load(open('.claude-plugin/marketplace.json')); json.load(open('plugins/qa/.claude-plugin/plugin.json')); print('OK')"
```

Expected: `OK`
