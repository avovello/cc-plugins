---
description: Generate automated test code from source code, requirements, or recorded browser actions
---

# Write Tests Command

Generate test code by analyzing source code. Creates unit, integration, or E2E tests with fixtures, mocks, and assertions.

## Usage

```bash
/write-tests [target]              # Generate tests for target
/write-tests --unit [file]         # Generate unit tests
/write-tests --integration [api]   # Generate integration tests
/write-tests --e2e [flow]          # Generate E2E tests
/write-tests --from-actions        # Convert recorded browser actions to tests
```

## Agent Orchestration

```
Task(subagent_type="qa:test-writer", prompt="Generate [unit/integration/e2e] tests for [target]")
```

## Test Generation Modes

### Unit Tests

```bash
/write-tests --unit src/services/user.ts
```

Generates: function tests, class method tests, mock configurations, edge case coverage.

### Integration Tests

```bash
/write-tests --integration /api/users
```

Generates: API endpoint tests, request/response validation, error handling, database setup/teardown.

### E2E Tests

```bash
/write-tests --e2e login
```

Generates: Playwright test specs, Page Object Models, user flow tests.

### From Recorded Actions

```bash
/write-tests --from-actions
```

Converts browser-tester recorded actions into Playwright tests.

## Output

```markdown
# Test Generation Report

## Generated Files
### 1. tests/unit/services/user.test.ts
- Tests: 8
- Coverage: UserService class

## Test Cases Generated
- [x] Happy path scenarios
- [x] Error handling
- [x] Edge cases
- [x] Input validation
```

## Options

| Option | Description |
|--------|-------------|
| `--unit` | Generate unit tests |
| `--integration` | Generate integration tests |
| `--e2e` | Generate E2E tests |
| `--from-actions` | From recorded browser actions |
| `--coverage` | Focus on coverage gaps |
| `--edge-cases` | Include edge cases |
