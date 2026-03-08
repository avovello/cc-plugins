---
name: unit-testing
description: Unit testing for isolated function and component testing. Use when running, writing, or reviewing unit tests, testing individual functions or class methods, mocking dependencies, or measuring code coverage with Jest, Vitest, pytest, or PHPUnit.
---

# Unit Testing

Guide for teammates working with unit tests — writing, running, or reviewing.

## Framework Detection

Check for configuration to determine the framework:

| File present | Framework | Run command |
|-------------|-----------|-------------|
| `jest.config.*` or `"jest"` in package.json | Jest | `npm test -- tests/unit/` |
| `vitest.config.*` | Vitest | `npx vitest run tests/unit/` |
| `pytest.ini` or `[tool.pytest]` in pyproject.toml | pytest | `pytest tests/unit/` |
| `phpunit.xml` | PHPUnit | `./vendor/bin/phpunit tests/Unit/` |

Add `--coverage` flag when coverage is requested.

## Writing Tests

Follow Arrange-Act-Assert. One assertion focus per test.

- Name tests descriptively: `should [expected behavior] when [condition]`
- Mock at boundaries — mock external deps, not internal implementation
- Use factories for test data — don't hardcode
- Test behavior, not implementation — tests shouldn't break on refactor

Match existing test conventions in the codebase (naming, directory structure, imports).

## Reviewing Tests

Check for:
- Coverage of happy path, error cases, boundary conditions
- Proper mocking strategy (no over-mocking)
- Descriptive test names
- Arrange-Act-Assert structure
- No hardcoded test data

## Coverage Targets

Statements 80%+, Branches 75%+, Functions 80%+, Lines 80%+.
