---
description: Run end-to-end tests using Playwright for browser automation
---

# E2E Command

Run Playwright-based end-to-end tests or launch interactive browser testing.

## Usage

```bash
/e2e                       # Run all E2E tests
/e2e [test-file]           # Run specific test file
/e2e [pattern]             # Run tests matching pattern
/e2e --headed              # Run with visible browser
/e2e --browser=firefox     # Run in specific browser
/e2e --debug               # Run in debug mode
/e2e --explore [url]       # Interactive browser testing
```

## Agent Orchestration

### Automated Tests

```
Task(subagent_type="qa:test-runner", prompt="Run Playwright E2E tests [options]")
```

### Interactive Testing (`--explore`)

```
Task(subagent_type="qa:browser-tester", prompt="Explore and test [url]")
```

## Browser Options

| Browser | Flag |
|---------|------|
| Chromium | `--browser=chromium` |
| Firefox | `--browser=firefox` |
| WebKit | `--browser=webkit` |
| All | `--browser=all` |

## Output

```markdown
# E2E Test Report

## Summary
- Total: 24 tests
- Passed: 22 (91.7%)
- Failed: 2 (8.3%)
- Duration: 2m 15s

## Failed Tests
1. login.spec.ts:23 - Timeout waiting for dashboard
   Screenshot: test-results/login-failed.png
```

## Options

| Option | Description |
|--------|-------------|
| `--headed` | Show browser window |
| `--debug` | Run in debug mode |
| `--browser=<name>` | Specific browser |
| `--workers=<n>` | Parallel workers |
| `--retries=<n>` | Retry failed tests |
| `--ui` | Interactive UI mode |
| `--explore` | Interactive browser testing |
