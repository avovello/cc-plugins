---
description: Run unit tests for isolated function and component testing
---

# Unit Command

Run unit tests. Detects the framework (Jest, Vitest, pytest, PHPUnit) and executes.

## Usage

```bash
/unit                      # Run all unit tests
/unit [file]               # Run specific test file
/unit [pattern]            # Run tests matching pattern
/unit --coverage           # Run with coverage report
/unit --watch              # Run in watch mode
```

## Agent Orchestration

```
Task(subagent_type="qa:test-runner", prompt="Run unit tests [options]")
```

## Framework Commands

| Framework | Command |
|-----------|---------|
| Jest | `npm test -- tests/unit/` |
| Vitest | `npx vitest run tests/unit/` |
| pytest | `pytest tests/unit/` |
| PHPUnit | `./vendor/bin/phpunit tests/Unit/` |

## Output

```markdown
# Unit Test Report

## Summary
- Total: 98 tests
- Passed: 97 (99%)
- Failed: 1 (1%)
- Duration: 12s

## Failed Tests
1. user.test.ts:45 - UserService.createUser
   Expected: "user@example.com"
   Received: undefined
```

## Options

| Option | Description |
|--------|-------------|
| `--coverage` | Generate coverage report |
| `--watch` | Watch mode for development |
| `--verbose` | Detailed output |
| `--bail` | Stop on first failure |
| `--update` | Update snapshots |
