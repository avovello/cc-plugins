# Test Command

Runs comprehensive test suites including unit, integration, and acceptance (E2E) tests.

## Usage

```bash
# Run all tests
/test

# Run specific test type
/test unit
/test integration
/test acceptance

# Run with coverage
/test --coverage

# Run in watch mode
/test --watch
```

## What It Does

1. Runs unit tests (fast, isolated)
2. Runs integration tests (component interactions)
3. Runs acceptance tests (E2E with Playwright)
4. Generates coverage reports
5. Creates test summary report

## Output

- Test results (pass/fail)
- Coverage reports
- Screenshots (for E2E failures)
- Performance metrics
- Summary report

## Test Execution Order

1. **Unit Tests** (fastest) → If fail, stop
2. **Integration Tests** (medium) → If fail, stop
3. **Acceptance Tests** (slowest) → Final validation

## Requirements

- Test framework installed (Jest, pytest, etc.)
- Playwright installed for E2E tests
- Test files present in project

## Configuration

Tests are discovered automatically in standard locations:
- `tests/`, `test/`, `__tests__/`
- `*.test.js`, `*.spec.ts`, `test_*.py`

## Next Steps

After tests complete:
- Review failed tests
- Check coverage reports
- Fix any issues
- Rerun tests
