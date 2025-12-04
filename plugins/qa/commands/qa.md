---
description: Comprehensive quality assurance workflow including unit, integration, and E2E tests
---

# QA Command

Runs comprehensive quality assurance workflow including unit, integration, and E2E tests.

## Usage

```bash
/qa                    # Run full QA suite
/qa [target]           # Run QA on specific area
/qa --coverage         # Run with coverage reports
/qa --quick            # Run unit tests only (fast)
```

## What It Does

The `/qa` command orchestrates a complete quality assurance workflow:

### 1. Unit Tests (Fast)
- Run isolated function/component tests
- Generate coverage report
- **Stop on failure** - Critical tests must pass

### 2. Integration Tests (Medium)
- Run API and database tests
- Verify component interactions
- **Stop on failure** - Integration issues block deployment

### 3. E2E Tests (Comprehensive)
- Run Playwright browser tests
- Test complete user workflows
- Capture screenshots/videos on failure

## Execution Order

```
Unit Tests → Integration Tests → E2E Tests
   (~10s)        (~1-2min)        (~3-5min)
```

Tests run sequentially - if unit tests fail, integration and E2E tests are skipped.

## Agents Used

| Phase | Agent | Purpose |
|-------|-------|---------|
| Unit | `test-runner` | Execute unit test suite |
| Integration | `test-runner` | Execute integration tests |
| E2E | `test-runner` | Execute Playwright tests |

## Output

```markdown
# QA Report

## Summary
- Unit Tests: 98 passed, 0 failed (12s)
- Integration Tests: 34 passed, 0 failed (45s)
- E2E Tests: 24 passed, 0 failed (2m 15s)
- Coverage: 87.3%

## Status: PASSED

## Details
[Detailed results from each phase]
```

## Options

| Option | Description |
|--------|-------------|
| `--coverage` | Generate coverage reports |
| `--quick` | Run unit tests only |
| `--verbose` | Show detailed output |
| `--bail` | Stop on first failure (default) |
| `--no-bail` | Run all tests even on failure |

## Requirements

- Test frameworks configured (Jest/Vitest, pytest, Playwright)
- Test files in standard locations
- Dependencies installed

## Examples

```bash
# Full QA suite
/qa

# Quick validation (unit tests only)
/qa --quick

# With coverage
/qa --coverage

# Test specific module
/qa auth
```
