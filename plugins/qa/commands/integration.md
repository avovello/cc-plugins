---
description: Run integration tests for API, database, and service testing
---

# Integration Command

Runs integration tests for API, database, and service testing.

## Usage

```bash
/integration               # Run all integration tests
/integration [file]        # Run specific test file
/integration [pattern]     # Run tests matching pattern
/integration --api         # Run API tests only
/integration --db          # Run database tests only
```

## What It Does

The `/integration` command executes integration tests:

1. **Setup Environment** - Prepare test database/services
2. **Run Tests** - Execute integration test suite
3. **Report Results** - Summary with failures
4. **Cleanup** - Reset test environment

## Agents Used

| Agent | Purpose |
|-------|---------|
| `test-runner` | Execute integration test suite |

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

## Test Categories
| Category | Passed | Failed |
|----------|--------|--------|
| API | 18 | 0 |
| Database | 12 | 1 |
| Services | 4 | 0 |

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

## Environment Setup

Integration tests may require:

```bash
# Start test database
docker-compose -f docker-compose.test.yml up -d

# Run migrations
npm run db:migrate:test

# Seed test data
npm run db:seed:test
```

## Examples

```bash
# Run all integration tests
/integration

# Run API tests only
/integration --api

# Run specific test file
/integration tests/integration/api/users.test.ts

# Run tests matching pattern
/integration users

# Database tests only
/integration --db
```

## Test Database

### Options

| Strategy | Pros | Cons |
|----------|------|------|
| In-memory SQLite | Fast | Limited features |
| Docker container | Realistic | Slower startup |
| Transactions | Fast cleanup | Complexity |

### Recommended Setup

```typescript
// tests/setup.ts
beforeAll(async () => {
  await db.migrate.latest();
});

beforeEach(async () => {
  await db.seed.run();
});

afterEach(async () => {
  await db('users').truncate();
});
```
