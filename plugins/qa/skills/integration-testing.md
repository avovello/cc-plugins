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
