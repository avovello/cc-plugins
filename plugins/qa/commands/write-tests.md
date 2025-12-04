---
description: Generate automated test code from requirements, source code, or recorded browser actions
---

# Write Tests Command

Generates automated test code from requirements, source code, or recorded browser actions.

## Usage

```bash
/write-tests [target]              # Generate tests for target
/write-tests --unit [file]         # Generate unit tests
/write-tests --integration [api]   # Generate integration tests
/write-tests --e2e [flow]          # Generate E2E tests
/write-tests --from-actions        # Convert recorded actions to tests
```

## What It Does

The `/write-tests` command generates test code:

1. **Analyze Target** - Read source code or requirements
2. **Identify Test Cases** - Determine what to test
3. **Generate Tests** - Create test files
4. **Add Edge Cases** - Include error scenarios
5. **Create Fixtures** - Generate test data

## Agents Used

| Agent | Purpose |
|-------|---------|
| `test-writer` | Generate test code |

## Test Generation Modes

### Unit Tests

```bash
/write-tests --unit src/services/user.ts
```

Generates:
- Function tests
- Class method tests
- Mock configurations
- Edge case coverage

### Integration Tests

```bash
/write-tests --integration /api/users
```

Generates:
- API endpoint tests
- Request/response validation
- Error handling tests
- Database setup/teardown

### E2E Tests

```bash
/write-tests --e2e login
```

Generates:
- Playwright test specs
- Page Object Models
- User flow tests
- Visual validation

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
- Includes: create, update, delete, find operations

### 2. tests/e2e/auth/login.spec.ts
- Tests: 4
- Scenarios: Valid login, invalid credentials, validation
- Page Objects: LoginPage

### 3. tests/factories/userFactory.ts
- Utilities: createUser, createUsers, createAdmin

## Test Cases Generated
- [x] Happy path scenarios
- [x] Error handling
- [x] Edge cases
- [x] Input validation

## Next Steps
1. Run tests with `/unit` or `/e2e`
2. Add additional scenarios if needed
3. Verify coverage targets met
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

## Examples

```bash
# Generate unit tests for a service
/write-tests --unit src/services/auth.ts

# Generate integration tests for API
/write-tests --integration /api/products

# Generate E2E tests for checkout flow
/write-tests --e2e checkout

# Convert recorded actions to tests
/write-tests --from-actions

# Generate tests for coverage gaps
/write-tests --coverage src/controllers/
```

## Workflow Integration

### Manual Testing → Automated Tests

```bash
# 1. Explore with browser-tester
/e2e --explore https://app.example.com/login

# 2. Record actions during exploration
# (browser-tester documents actions)

# 3. Generate tests from recorded actions
/write-tests --from-actions

# 4. Run generated tests
/e2e
```

### Coverage-Driven Test Generation

```bash
# 1. Run tests with coverage
/unit --coverage

# 2. Generate tests for uncovered code
/write-tests --coverage src/services/
```
