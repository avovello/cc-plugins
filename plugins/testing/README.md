# Testing Plugin 🧪

Comprehensive testing workflows covering unit tests, integration tests, and end-to-end acceptance tests with Playwright + TypeScript.

## Installation
```bash
/plugin install testing
```

## Usage
```bash
# Run all tests
/test

# Run specific test type
/test unit
/test integration
/test acceptance
```

## Specialized Agents (3)
1. **unit-tester**: Runs and reports unit test results
2. **integration-tester**: Runs and reports integration test results
3. **acceptance-tester**: Runs E2E tests using Playwright + TypeScript for real web applications

## Test Types

### Unit Tests
- Test individual functions/methods in isolation
- Fast execution (milliseconds)
- High coverage of business logic
- Mocked dependencies

### Integration Tests
- Test component interactions
- Database, API, service integration
- Moderate execution time (seconds)
- Real or test dependencies

### Acceptance Tests (E2E)
- Test complete user workflows
- Real browser automation (Playwright)
- TypeScript for type safety
- Cross-browser testing
- Slower execution (seconds to minutes)
- Full system integration

## Workflow
Run Unit → Run Integration → Run Acceptance (E2E) → Generate Reports

## Technologies
- Unit/Integration: Jest, Mocha, pytest, PHPUnit (framework-specific)
- Acceptance: **Playwright + TypeScript**
- Reporting: HTML reports, coverage reports, screenshots

## Version
0.1.0
