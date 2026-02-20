---
description: Run the full test suite: unit, integration, and E2E tests in sequence
---

# QA Command

Run the full test suite in order: unit → integration → E2E. Stops on failure by default.

## Usage

```bash
/qa                    # Run full test suite
/qa --coverage         # Run with coverage reports
/qa --quick            # Run unit tests only (fast)
/qa --no-bail          # Run all tests even on failure
```

## Execution Order

```
Unit Tests → Integration Tests → E2E Tests
   (~10s)        (~1-2min)        (~3-5min)
```

If unit tests fail, later phases are skipped (unless `--no-bail`).

## Agent Orchestration

```
Task(subagent_type="qa:test-runner", prompt="Run unit tests")
Task(subagent_type="qa:test-runner", prompt="Run integration tests")
Task(subagent_type="qa:test-runner", prompt="Run E2E Playwright tests")
```

## Output

```markdown
# QA Report

## Summary
- Unit Tests: 98 passed, 0 failed (12s)
- Integration Tests: 34 passed, 0 failed (45s)
- E2E Tests: 24 passed, 0 failed (2m 15s)
- Coverage: 87.3%

## Status: PASSED / FAILED
```

## Options

| Option | Description |
|--------|-------------|
| `--coverage` | Generate coverage reports |
| `--quick` | Run unit tests only |
| `--verbose` | Show detailed output |
| `--bail` | Stop on first failure (default) |
| `--no-bail` | Run all tests even on failure |
