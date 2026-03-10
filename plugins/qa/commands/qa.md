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
