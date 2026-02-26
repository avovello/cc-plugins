---
description: Systematic bug fixing with reproduction, root cause analysis, and team-based fix+review loops
allowed-tools: Task, Bash
---

# Bugfix Command

Investigate a bug report, identify the root cause, and fix it using team-based do-loops for implementation and regression testing.

> **Prerequisite**: Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` for Phase 2 and Phase 3.

## Usage

```bash
/bugfix "Login fails with special characters in password"
```

## Workflow

### Phase 1: Investigation (subagents)

Sequential investigation to reproduce the bug and identify its root cause.

#### Step 1: Reproduce the Bug

```
Task(subagent_type="bugfix:reproducer", prompt="Reproduce this bug: [bug description]")
```

The reproducer will:
- Find relevant code paths
- Create a failing test or document exact reproduction steps
- Report reproduction evidence and relevant code locations

If reproduction fails, ask the user for more context before proceeding.

#### Step 2: Root Cause Analysis

```
Task(subagent_type="bugfix:root-cause-analyst", prompt="Analyze root cause given this reproduction report: [reproduction results]")
```

The root-cause-analyst will:
- Trace the execution path from reproduction evidence
- Identify and classify the root cause
- Assess impact and related patterns
- Propose a fix with specific files and changes

#### Step 3: Present Findings

Present the reproduction report and root cause analysis to the user. Summarize:
- What the bug is and how it was reproduced
- The root cause and its classification
- The proposed fix approach and files involved
- Any risks or related patterns

**PAUSE**: Wait for user confirmation before proceeding to the fix phase.

### Phase 2: Fix Do-Loop (agent team)

Load the `do-loop` skill. Spawn a doer+critic pair to implement and review the fix.

Start a team of 2:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| fixer | Implement the fix per root cause analysis. Minimal changes, match existing code style. | opus | Reproduction report + root cause analysis + fix proposal + relevant file paths |
| reviewer | Review the fix for correctness, edge cases, regressions, and code style. | opus | Same context + do-loop iteration protocol (max 3 rounds, structured feedback format, exit conditions) |

**Task**: `Implement fix for [bug title] — [acceptance criteria from fix proposal]`

**Done means**: The fix resolves the root cause, handles identified edge cases, and matches existing code style.

**Quality bar for the reviewer**:
- Fix addresses the root cause, not just the symptom
- No new edge cases introduced
- Error handling covers failure modes identified in the analysis
- Changes are minimal and focused on the bug

Iterate per do-loop protocol (max 3 rounds). If the reviewer and fixer deadlock, you (the team lead) decide based on the root cause analysis.

### Phase 3: Regression Test Do-Loop (agent team)

Load the `regression-test` skill. Spawn a new doer+critic pair for test writing.

Start a team of 2:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| test-writer | Write regression test for the fixed bug. Cover the exact scenario, edge cases, and related patterns. | opus | Fix changes + reproduction report + regression-test skill guidance |
| test-reviewer | Review test quality: naming, coverage, assertions, placement. | opus | Do-loop iteration protocol (max 2 rounds, structured feedback format, exit conditions) |

**Task**: `Write regression tests for [bug title] — cover exact scenario + edge cases`

**Done means**: Tests would fail before the fix, pass after, and cover boundary conditions.

**Quality bar for the test-reviewer**:
- Test names describe the bug scenario, not the fix
- Tests follow Arrange-Act-Assert structure
- Assertions verify correct behavior, not just "doesn't crash"
- Tests are placed near related existing tests

One mini-loop (1-2 rounds). The test-reviewer approves once coverage and quality are adequate.

### Phase 4: Verify and Summarize

After both do-loops complete:

1. Run the regression tests and related test suites to confirm everything passes
2. Run `git diff` to review all changes

Present the summary to the user:

```markdown
## Bugfix Summary

### Bug
[Bug title and description]

### Root Cause
[1-2 sentence root cause explanation]

### Fix
[What was changed and why]

### Files Changed
- `path/to/file.ext` — [what changed]

### Tests Added
- `path/to/test.ext` — [what's covered]

### Verification
- [x] Regression tests pass
- [x] Related tests pass
- [ ] Ready for commit
```

The user decides whether to commit.

## Agents

| Agent | Purpose |
|-------|---------|
| `bugfix:reproducer` | Reproduce the bug, create failing test |
| `bugfix:root-cause-analyst` | Trace execution, identify root cause, propose fix |

## Skills

| Skill | Purpose |
|-------|---------|
| `do-loop` | Iteration protocol for doer+critic agent team loops |
| `regression-test` | How to write effective regression tests |
