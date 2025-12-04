---
description: Run unit tests for isolated function and component testing
---

# Unit Command

Runs unit tests for isolated function and component testing.

## Usage

```bash
/unit                      # Run all unit tests
/unit [file]               # Run specific test file
/unit [pattern]            # Run tests matching pattern
/unit --coverage           # Run with coverage report
/unit --watch              # Run in watch mode
```

## What It Does

The `/unit` command executes unit tests:

1. **Detect Framework** - Jest, Vitest, pytest, PHPUnit
2. **Run Tests** - Execute unit test suite
3. **Generate Coverage** - Code coverage report (if requested)
4. **Report Results** - Summary with failures

## Agents Used

| Agent | Purpose |
|-------|---------|
| `test-runner` | Execute unit test suite |

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

## Coverage
| Category | Statements | Branches | Functions |
|----------|------------|----------|-----------|
| Services | 92.1% | 85.3% | 88.9% |
| Utils | 98.2% | 95.0% | 100% |
| Total | 87.3% | 82.4% | 88.2% |

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

## Examples

```bash
# Run all unit tests
/unit

# Run with coverage
/unit --coverage

# Run specific file
/unit tests/unit/services/user.test.ts

# Run tests matching pattern
/unit user

# Watch mode for development
/unit --watch

# Run only failed tests
/unit --failed
```

## Coverage Targets

| Metric | Target | Description |
|--------|--------|-------------|
| Statements | 80%+ | Lines of code executed |
| Branches | 75%+ | Conditional paths tested |
| Functions | 80%+ | Functions called |
| Lines | 80%+ | Actual lines covered |
