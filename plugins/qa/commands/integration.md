---
description: Run integration tests for API, database, and service testing
---

# Integration Command

Run integration tests for API endpoints, database operations, and service interactions.

## Usage

```bash
/integration               # Run all integration tests
/integration [file]        # Run specific test file
/integration [pattern]     # Run tests matching pattern
/integration --api         # Run API tests only
/integration --db          # Run database tests only
```

## Agent Orchestration

```
Task(subagent_type="qa:test-runner", prompt="Run integration tests [options]")
```

## Test Types

| Type | Description | Location |
|------|-------------|----------|
| API | HTTP endpoint tests | tests/integration/api/ |
| Database | CRUD operation tests | tests/integration/db/ |
| Service | External service tests | tests/integration/services/ |

## Output

```markdown
# Integration Test Report

## Summary
- Total: 34 tests
- Passed: 33 (97%)
- Failed: 1 (3%)
- Duration: 45s

## Failed Tests
1. users.test.ts:78 - POST /api/users validation
   Expected: 400 Bad Request
   Received: 500 Internal Server Error
```

## Options

| Option | Description |
|--------|-------------|
| `--api` | API tests only |
| `--db` | Database tests only |
| `--services` | Service tests only |
| `--verbose` | Detailed output |
| `--bail` | Stop on first failure |
